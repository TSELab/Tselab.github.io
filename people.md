---
layout: home
title: People
---
<div style="overflow: hidden; padding: 20px">
	<h2>Faculty</h2>
	{% for item in site.data.people.faculty %}
	<div class='people-card'>
	  <img src="/assets/images/people/{{ item.photo }}" /><br/>
	  {{ item.name }}<br/>
	  {{ item.title }}<br/>
	  <a href="{{ item.website }}">website</a>
	</div>
	{% endfor %}
</div>
<div style="overflow: hidden; padding: 20px">
	<h2>Students</h2>
	{% for item in site.data.people.students%}
	<div class='people-card'>
	  <img src="/assets/images/people/{{ item.photo }}" /><br/>
	  {{ item.name }}<br/>
	  {{ item.title }}<br/>
	  <a href="{{ item.website }}">website</a>
	</div>
	{% endfor %}
</div>
<div style="overflow: hidden; padding: 20px">
	<h2>Staff</h2>
	{% for item in site.data.people.staff%}
	<div class='people-card'>
	  <img src="/assets/images/people/{{ item.photo }}" /><br/>
	  {{ item.name }}<br/>
	  {{ item.title }}<br/>
	  <a href="{{ item.website }}">website</a>
	</div>
	{% endfor %}
</div>
