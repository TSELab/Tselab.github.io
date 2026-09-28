---
layout: none 
title: Publications 
permalink: /publications/
publication_sections:
  - key: conference
    title: Conference Papers
  - key: journal
    title: Journal Articles
  - key: other
    title: Workshop & Magazine Articles
  - key: report
    title: Technical Reports
  - key: preprint
    title: Preprints
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
            <div class="transparencia-titulo" id="transparencia-projects">
                <h1 class="title roboto-normal-white-70px">Publications</h1>
            </div>
          </div>
          <div class="publications-2">
              {% for section in page.publication_sections %}
              {% assign publications = site.data.publications[section.key] | sort: 'year' | reverse %}
              {% if publications.size > 0 %}
              <div class="publication-type-header">{{ section.title | escape }}</div>
              {% for pub in publications %}
              <div class="publication-entry">
                <div class="publication-title roboto-normal-dove-gray-20px">
                  <a href="{{ pub.url | escape }}" class="roboto-normal-dove-gray-20px">{{ pub.title | escape }}</a>
                </div>
                <div class="publication-author-list roboto-normal-tuatara-18px">
                  {{ pub.info | escape }}
                </div>
                {% if pub.pdf %}
                <div class="publication-links roboto-normal-tuatara-18px">
                  <a href="{{ pub.pdf | escape }}" aria-label="{{ pub.pdf_label | default: 'PDF' | escape }}: {{ pub.title | escape }}">[{{ pub.pdf_label | default: 'pdf' | escape }}]</a>
                </div>
                {% endif %}
                {% if pub.award %}
                <div class="publication-award roboto-normal-tuatara-18px">
                  <svg class="publication-award-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true" focusable="false">
                    <path d="M8.5 13 7 22l5-3 5 3-1.5-9" />
                    <circle cx="12" cy="8" r="6" />
                  </svg>
                  <a href="{{ pub.award_url | escape }}">{{ pub.award | escape }}</a>
                </div>
                {% endif %}
              </div>
              {% endfor %}
              {% endif %}
              {% endfor %}
          </div>
        </div>
      </div>
    </div>
  </body>
</html>
