---
layout: post
title:  "Looking at the Git landscape through SHATTERED glass"
date:   2017-03-04 21:00:00 -0400
categories: git sha1 rant
---

A recent [blogpost](https://security.googleblog.com/2017/02/announcing-first-sha1-collision.html) 
from Google and CWI showed us what many had suspected would happen soon: a
practical attack on SHA-1 could be successfully carried out.  Although this is
an important milestone for the history of cryptographic hash algorithms (if
that’s even a thing), the practical implications are more nuanced. As it is
with the emerging trend of branded vulnerabilities — (this one is called
[shattered](https://shattered.io/)) — the details are lost in a sea of PR-littered vacuity and witty
names for vulnerabilities.

Among the long list of “broken” applications, there is Git, probably the most
widely used version control system. Given this popularity, it is not surprising
that many people have been running around flailing their arms, writing [risible
headlines](https://arstechnica.com/security/2017/02/watershed-sha1-collision-just-broke-the-webkit-repository-others-may-follow/)
and the tweeting the
[not](https://twitter.com/bcrypt/status/834762918692483073)–[so](https://twitter.com/bascule/status/836298123408388096)–[amusing](https://twitter.com/andywingo/status/835132154749272064)–[anymore](https://twitter.com/realhashbreaker/status/83519994580423884://twitter.com/realhashbreaker/status/835199945804238848)
“securely holier than thou”
[tweets](https://twitter.com/NathOnSecurity/status/834796736308793344).

At first sight, the concern is well founded. A collision in SHA-1 allows
attackers to replace files in such a way that git — not even with signing
enabled — cannot identify it. Of course, this would let resourceful attackers
sneak in backdoored versions of files after a repository compromise, or create
a signed commit object that wasn’t made by the author of the signature.
However, after further inspection, the picture is not that grim.

The truth is, as I will show below, SHA-1 is broken in such a way that
performing any of these attacks is unfeasible; at least from the economic
standpoint. Throughout this post, I’ll show how a doomsday scenario would be
for git if someone really, really wanted to attack by colliding hashes.

## Background (or “wait, wasn’t this already very, really broken?!”)

Before digging into the details of git and its use of SHA-1, I wanted to make a
brief rundown of what actually happened this last Thursday, and try to place it
in the context of other hash functions.

The truth is, SHA-1 was already broken, as it was announced by [rivest](https://mail.python.org/pipermail/python-dev/2005-December/058850.html) back in 2005. 
The reason for this conclusion is that, although the name is not similar,
the construction is practically the same as MD5. This construction,
Merkle-Damgard, is the one used by both hash algorithms and the reason an
“arbitrary prefix attack” is possible. It is of no surprise that some rockstars
in the industry have all came out, unsurprised themselves, to point out SHA-1
was already deprecated by NIST 6 years ago.

This comparison with MD5 is a really good starting point to understand the
nature of these “news” and how they apply to the use of SHA-1. Here is the
timeline of the practical attacks against MD5:

1. In 1996, collisions were found in the compression function of MD5. Since
   then, experts recommended to stay away from MD5.
2. In 2005, researchers were able to create a Postscript file that collided.
   When this happened, Rivest came out to [declare both MD5 and SHA-1 broken in
   terms of collision resistance](https://mail.python.org/pipermail/python-dev/2005-December/058850.html).
3. In 2007, Marc Stevens (sounds familiar), wrote hashclash, which [Nat McHugh](https://natmchugh.blogspot.com/2014/10/how-i-created-two-images-with-same-md5.html)
   used to collide the hash of two PNG files.
4. In 2008, [CCC were able to impersonate a CA by colliding their MD5 hashes](https://arstechnica.com/security/2008/12/theoretical-attacks-yield-practical-attacks-on-ssl-pki/).
5. In 2012, [The flame malware used an MD5 collision](https://arstechnica.com/security/2012/06/flame-wields-rare-collision-crypto-attack/) to fake a certificate owned by microsoft.
6. In 2017, [people still use MD5, god knows why](https://blogs.oracle.com/stevenChan/entry/jar_md5).

As I hinted before, the timeline for SHA-1 is (and will be) incredibly similar
to the one for MD5, with the current events matching the ones for the 
year 2005. A collision in a PS/PDF file is pretty much the same, as these file
formats are not brittle as others, and they allow random data to be located
somewhere in the file without showing it or damaging the file format in any
way. Other file formats, such as X.509 certificates, source code files and
lossy-compression images are not so resilient, which greatly downplays the
possibility of a collision. I’ll elaborate more on this fact later.

Other similarities also arise; approximately 10 years after people declared MD5
broken, it was broken in practice. This time, it took us 12 years to go from
warning to be able to hash two files and get the exact same value. The final
similarity is rather obvious: thanks to Moore’s law and further research,
attacks are only going to become more effective. There is no reason to continue
using SHA-1 for newer applications.

The lesson to take from this comparison is that this marks a milestone in the
usual life of a hash algorithm, it may put a nail in the coffin, but the
algorithm is not completely dead for applications that rely on it today (as it
is the case of git).

Regardless of this fact, we wanted to do a doomsday scenario for git, so you
will have it. To do this, I have to give you a little bit of background on git.

## How does git work?

The information we need from git to carry out our attack is minimal. I need to
describe the file formats, and then from that we will pick the best point to
wreak havoc in a git repository (not really, just on paper).

A git repository is mostly made of two types of files: references and objects.
To keep things brief, I’ll skip the details of the former. It suffices to say
that git references, like in a programming language, are pointers to other
entities. In this case, they are pointers to git objects. An example of a
reference is branches, who point to git commit objects.

Git objects hold information about a repository. As of today, there are four of
them:

- Git commit objects: these hold information about a revision in the
  repository. Inside a commit object you will find the author, the commit
  message, a date, and other stuff. However, the most important bit of
  information is the id of a root tree object.
- A tree object is akin to a folder in a filesystem, and it contains a listing
  of other tree objects and blob objects (among with information about them, as
  it shown below).
- A blob object which, as you may have guessed, is a file. This contains the
  size of the file and the contents of the file itself.
- Finally, there are tag objects, which are pretty much like commit objects,
  but they are meant to point to a static position in the repository(e.g.,
  release v1.0). Git tags are usually signed using GPG to ensure the
  authenticity of the tag and all the files to which it is indirectly pointing
  to.

It is in the ID of all of these objects, where the SHA-1 function is used. To
obtain the id of an object, you simply hash the contents and header of the
object to obtain the id. By creating a new object that shares an SHA-1 with a
known object, we can perform our attack.

For example:

1. We could create a new commit object with the same id and replace it. This
   would let us replace the whole root tree and pretty much serve another
   repository.
2.  We would also create a new root tree object with the same id and replace
    it. This would let us replace all or any of the files in the repository.
3. We could also create a file that hashes to the same value (with a minor
   caveat that I’ll cover later) and replace the object in the repository.
4. We could do the same thing we do for a commit as we do for a git tag.

From the attacks that I outlined above, only the third is really feasible. The
reason as to why this is lies in the fact that the git commit/tree object
format is rather brittle. Unlike PDF’s and PS files, you cannot put many bytes
of random junk somewhere and expect git to not complain about a corrupt object.

This pretty much leaves us with the ability to replace blobs, which should be
enough to do something evil. Let me elaborate on this a little bit more.

## Random Junk, and the probability of getting the right Junk

When carrying the attacks described in the paper the researchers exploited the
fact that the Merkle-Damgard construction is likely to produce the same hash if
these two files share a common prefix. For this, you need to set a common
prefix between both files.

Immediately after, it comes two blocks of random bytes. The first block is is
used to make the hash function reach a certain “state” called “near-collision.”
The second block of random bytes is used to cause a complete collision that
also allows for any kind of suffix. These blocks apparently need to be found
only once, so you can create your own colliding pdf’s using these blocks.

However, these collisions require the same prefix-length, a chosen prefix and a
place to locate 84 bytes of junk after the prefix in such a way that it doesn’t
result in a corrupted file. It is the placement of this junk what gets in the
way with brittle file formats. For example, for git trees, any byte that falls
out of the format for a file list would be corrupted:


    $ cat .git/objects/da/3d3fc569dc3ded6c67e5209840ff4205202613 | zlib-flate -uncompress | hexdump -C
    00000000 74 72 65 65 20 31 33 37 00 31 30 30 37 35 35 20 |tree 137.100755 |
    00000010 74 68 65 5f 73 6f 6f 74 68 69 6e 67 5f 73 6f 75 |thesoothingsou|
    00000020 6e 64 5f 6f 66 5f 68 61 73 68 5f 63 6f 6c 6c 69 |ndofhashcolli|
    00000030 73 69 6f 6e 73 2e 70 79 00 45 ca db ed b6 09 fd |sions.py.E......|
    00000040 cc 29 c5 5b 79 54 83 ba 6a c6 b1 f9 b0 31 30 30 |.).[yT..j....100|
    00000050 36 34 34 20 74 68 65 5f 73 6f 6f 74 68 69 6e 67 |644 thesoothing|
    00000060 5f 73 6f 75 6e 64 5f 6f 66 5f 68 61 73 68 5f 63 |soundofhashc|
    00000070 6f 6c 6c 69 73 69 6f 6e 73 2e 77 61 76 00 0e 0a |ollisions.wav...|
    00000080 e5 a7 bf 61 78 d4 90 12 7b 2c 74 0e 78 34 b0 85 |...ax...{,t.x4..|
    00000090 cf 59 |.Y|
    00000092


So that a tree object is correct, you would need:

- The ascii word tree (this could easily be a prefix), followed by a space, the size and a null
- Then a list of entries, consisting of:
    - 6 decimal ascii digits, for permission and file-type followed by a space
    - the filename followed by a null
    - 20 bytes that point to another existing git object (submodules are repesented by a commit)
    - and then, immediately, the next entry

That’s it, that’s what appears in a tree object. If we were to place 84 bytes
of random junk somewhere there, we would have to be really lucky to find
colliding blocks that match (we can’t use the ones for pdf’s) the format for a
tree. The probability is pretty much 0.

A more realistic approach would be to repeat this with a blob object. However,
this is still sensitive to the file format that we use. The header of a blob
object is only the size of the blob (in bytes) and a null, followed by the rest
of the content. Due to this small header, we can’t use the same 
[colliding blocks for the pdfs](https://github.com/joeyh/supercollider)

Luckily for us (remember we are attackers), it is feasible to account for this
header and find a new collision. Indeed, the easy way to do this would be to
just prepend the size of the file and a newline to the pdf prefix and recompute
the collision. Causing collisions for other file formats would be harder — the
probability to find a two blocks of 84 + 64 printable bytes (assuming an even
distribution) to create a “meaningful” source code file floats 
around 97/256^100 = 1.454705909478762e-239 if there were only 100 bytes in total.

A colliding C file may look like this.

    /* prefix stuff */
    include <...>
    int main (...) {
      /* BLOCK1 of ascii-printable crap
       *
       */

      /* BLOCK2 of ascii-printable crap
       *
       */

       evil_code(); // not part of the collision.

    }

Notice we had to be super lucky to not have any nulls inside the comment, or
control characters, or the sequence */ that would terminate the comment early.

Now, let’s assume we are that lucky, and that we are still willing to collide a
file, any file. We would still need to consider other factors. The first one
that came to mind is blob-lifetime.

## Blob lifetime

Blob lifetime is what I dubbed as the time between a blob’s inception to the
time it is replaced by another, newer blob. This is particularly relevant
because, if you are going to spend a year like these researchers did in finding
a collision, maybe you want to collide a blob that is not going to be unused by
the time you find it, which may possibly replace the prefix. To collide an
arbitrary blob, an attacker should:

1. Take the blob header. This is, size + newline as a prefix, plus other things
   that are usually static (e.g, some of the imports, maybe the license on the
   comments, etc.)
2. Compute the colliding blocks immediately after in such a way that it doesn’t
   corrupt the file (here’s where they would have to get really lucky if this
   was code).
3. Once finding a collision, pad with whatever backdoor code they’d like and
   create the evil blob. Another non-evil blob should be sent to the repository
   to create a valid entry, maybe through a non-malicious pull request,
   although you would need a good explanation for your two block comments with
   random ascii characters.

On the graph below you can explore the lifetime of blob objects for some
popular git repositories (if you’d like me to compute a dataset with your repo
please ping me!). You can explore the files, where they appear and see the time
it took for them to be replaced. I did a linear equation to “estimate” the cost
of cracking a certain blob that (if we were able to know how long would a blob
survive before it was replaced) you can hover.

<iframe src="https://santiagotorres.github.io/blob_lifecycles/?dataset=git.json" width="100%" height="720"></iframe>

You can see a bigger version of this graph [here](https://santiagotorres.github.io/blob_lifecycles/?dataset=git.json)

This graph is pretty interesting due to many factors. It sheds light on the low
hanging fruit that an attacker would maybe exploit. The files that are easy to
crack are for example:

- LICENSE files: boring
- Test code: also boring
- Documentaton: Also boring
- Vendored code (check the rightmost delta on the docker dataset): somewhat interesting
- Images: I guess we could prank someone by replacing their logo with their
  childhood pictures by spending 110 GPU years to cause a collission or
  something.

The second exercise that I’d like you to do is the following: pick any file
that you would like to crack on the rightmost side, and then write the name of
that file in the filter. You will most likely see that the file appears also on
the left. The reason as to why this happens is that, as projects grow, the code
churn increases with it. The newer versions of the files (and the ones that are
used on the latest revision) usually fall on the left margin.

Result: you may be able to change the LICENSE file of a single-developer
project by spending 31k and two and a half years.

## Sneaking the blob

Now, let’s say that we got lucky with the blob, we spent our tuition money and
two years of our life on because we really wanted to screw that guy’s
.gitignore file. We did it, we have a blob that hashes to the same thing, now
what? The story is not really over.

The way the git transport protocol works doesn’t let us replace the remote
blob. I won’t go into the details, but a cartoonish depiction of what a push
would look like is as follows:

    > CLIENT: I'm pushing this commit, with this tree, and here's this new blob with commit id 0xCAFEC0FFEE
    > SERVER: Oh, I already got 0xCAFEC0FFEE, but thanks
    > CLIENT: =(

What alternatives do we have? keeping the blob locally is useless, because we
want people to use our blob. To do so, I’m imagining the following
alternatives:


- We could destroy all history on the git repository that contains all mentions
  of the blob, and wait for it to get garbage-collected, and then push again —
  noisy
- We could, instead, trick people to get their stuff from our repository — we
  need to be known and this would be pretty obvious
- We could man in the middle the connection — Oh, why don’t we break GitHub’s
  certificate fingerprints? Sadly, they don’t use SHA-1 anymore so we can’t do
  that…
- We could break into the repository, and change the file manually.

The story would be maybe different if the repository owner was the one acting
maliciously but, if he’s not a third party, he could just replace the history.
This may be the most dangerous case, for he could replace blobs for as long as
the prefix doesn’t change.

To be fair, of all of the highly unlikely things that I’ve listed here, at
least the third and fourth seem plausible. Let’s assume you did that. At this
point, you successfully hacked a git repository by causing a SHA-1 collision.

Note, I presented this as an thought exercise, for the git folks are already
working on integrating the hardened SHA-1 code and your attack would
unsuccessful once it is merged. Shucks!

To summarize:

- This attack is an important milestone in the evolution of SHA-1’s
  deprecation.
- This attack is not feasible to do against git. An arbitrary prefix attack
  would be more interesting, but we aren’t there yet.
- Even if it was, collisions of certain files are harder than others, given the
  code churn and the entropy of the repository’s files.
- You would have to be really lucky to find two colliding blocks for files that
  have brittle file formats (such as code or certain git objects).
- Say you were lucky, there are better ways to mess with people’s LICENSE files
- To do some damage, you would target vendored code, but vendored code is
  already a mess that should be handled better. There are other ways to abuse
  this fact, just look at libtiff, zziplib and libwmf.
- The story is not over one you compute the collision. You have to put the blob
  in the right place. This often means either stealing a certificate or hacking
  into a server. The story is of course different if you are a malicious
  server, but things can go wrong in many other ways if that happens.
- There are already works in the making to both harden git’s use of sha1 and
  replace the hashing algorithm, so don’t get your hopes up.
