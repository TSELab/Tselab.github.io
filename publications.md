---
layout: none 
title: Publications 
permalink: /publications/
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
                <h1 class="title roboto-normal-white-70px">Publications</h1>
            </div>
          </div>
          <div class="publications-2">
              <div class="publication-type-header">Conference Papers</div>
              {% for pub in site.data.publications.conference %} 
              <div class="publication-entry">
                <div class="publication-title roboto-normal-dove-gray-20px">
                  <span class="roboto-normal-dove-gray-20px">{{ pub.title }}</span>
                </div>
                <div class="publication-author-list roboto-normal-tuatara-18px">
                    {{ pub.info }}
                </div>
              </div>
              {% endfor %}
              <div class="publication-type-header">Journal Articles</div>
              <div class="publication-entry">
                <div class="publication-title roboto-normal-dove-gray-20px">
                  <span class="roboto-normal-dove-gray-20px">&#34;In-toto: providing farm-to-table security guarantees for bits and bytes.”</span>
                </div>
                <div class="publication-author-list roboto-normal-tuatara-18px">
                  S. Torres-Arias, H. Afzali, T. K. Kuppusamy, R. Curtmola, J. Cappos. 28th USENIX Security Symposium
                  (USENIX Security ‘19) Santa Clara, CA 2019.
                </div>
              </div>  
              <div class="publication-type-header">Workshop & Magazine Articles</div>
              <div class="publication-entry">
                <div class="publication-title roboto-normal-dove-gray-20px">
                  <span class="roboto-normal-dove-gray-20px">&#34;In-toto: providing farm-to-table security guarantees for bits and bytes.”</span>
                </div>
                <div class="publication-author-list roboto-normal-tuatara-18px">
                  S. Torres-Arias, H. Afzali, T. K. Kuppusamy, R. Curtmola, J. Cappos. 28th USENIX Security Symposium
                  (USENIX Security ‘19) Santa Clara, CA 2019.
                </div>

          </div>
        </div>
      </div>
    </div>
  </body>
</html>
