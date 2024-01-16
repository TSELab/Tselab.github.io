---
layout: none
title: People
permalink: /people/
---
<html>
  {%- include head.html -%}
  <body style="margin: 0">
    <input type="hidden" id="anPageName" name="page" value="tslab" />
    <div class="container-center-horizontal">
      <div class="tslab screen">
        {%- include header.html -%}
        <div class="overlap-group15">
          <!--<div class="overlap-group10">
            <div class="transparencia-titulo" id="transparencia-projects">
                <h1 class="title roboto-normal-white-70px">People</h1>
            </div>
          </div> -->
          <div class="people-2">
            <div class="people-container">
              <div class="people-list">
                  <div class="people-title">Faculty</div>
                  <div class="break"></div>
                  {% for member in site.data.people.faculty %} 
                  <div class="people-card">
                    <img class="people-photo" src="/assets/images/people/{{ member.photo }} " />
                    <div class="name">{{ member.name }} </div>
                    <div class="people-position"> {{ member.position }} </div>
                    <div class="people-interests">Research Interests:<br />  {{ member.interests }}</div>
                    <a href="https://badhomb.re/"> <div class="read-more-2">website</div></a>
                  </div>
                  {% endfor %}
              </div>
              <br/>
              <div class="people-list">
                  <div class="people-title">Students</div>
                  <div class="break"></div>
                  {% for member in site.data.people.students %} 
                  <div class="people-card">
                    <img class="people-photo" src="/assets/images/people/{{ member.photo }} " />
                    <div class="name">{{ member.name }} </div>
                    <div class="people-position"> {{ member.position }} </div>
                    <div class="people-interests">Research Interests:<br />  {{ member.interests }}</div>
                    <a href="https://badhomb.re/"> <div class="read-more-2">website</div></a>
                  </div>
                  {% endfor %}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </body>
</html>
