---
layout: none 
title: Talks
permalink: /talks-and-media/
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
                <h1 class="title roboto-normal-white-70px">Talks & Media</h1>
            </div>
          </div>
        </div>
        <div class="talks-and-media-2">
        
        {% for talk in site.data.talks%} 
          <div class="overlap-group">
            <img class="rectngulo-negro" src="/assets/img/rect-ngulo-negro-2-f@1x.svg" />
            <img class="rectangulo-caf" src="/assets/img/rectangulo-caf--2-f@2x.svg" />
            <a href="{{ talk.url }}">
                <div class="docker-con-1 roboto-normal-white-18px">{{ talk.title}} </div>  
            </a>
            <div class="text-8 roboto-bold-dove-gray-20px-2">“{{ talk.venue}}”</div>
          </div>
        {% endfor %}
      </div>
    </div>
  </body>
</html>
