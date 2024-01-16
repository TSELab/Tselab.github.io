---
layout: none
title: Projects
permalink: /projects/
---
<html>
  {%- include head.html -%}
  <body style="margin: 0">
    <input type="hidden" id="anPageName" name="page" value="tslab" />
    <div class="container-center-horizontal">
      <div class="tslab screen">
        {%- include header.html -%}
        <div class="overlap-group15">
          <div class="overlap-group8">
            <div class="transparencia-titulo" id="transparencia-projects"/>    
                <h1 class="title roboto-normal-white-70px">Projects</h1>
            </div>
          </div>
          <div class="overlap-group9">
          
            {% for project in site.data.projects %} 
            <div class="project-card">
              <div class="project">{{ project.name }}</div>
              <div class="text-23 roboto-normal-tuatara-18px">
                {{ project.description }}
              </div>
              <a href="{{ project.link }} "><div class="read-more">Read more</div></a>
              <img class="project-diagram" src="/assets/img/{{ project.image}}" />
            </div>
            {% endfor %} 
          </div>
      </div>
    </div>
  </body>
</html>
