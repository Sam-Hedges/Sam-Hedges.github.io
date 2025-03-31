---
layout: default
permalink: /
---

{% include landing.html %}

<h1 align="center" class="wow animated slideInUp" data-wow-delay=".15s" id="-heading-"><strong>📌 Pinned</strong></h1>

<div class="card-columns m-3 mt-5">

  <!-- Local Projects -->
  {% for project in site.projects %}
    {% if project.pinned %}  <!-- Filter only pinned projects -->

      {%- assign project_type  = "local" -%}
      {%- assign project_id    = project.name | slugify -%}
      {%- assign project_img   = project.image -%}
      {%- assign project_name  = project.name -%}
      {%- assign project_desc  = project.description -%}
      {%- assign project_tools = project.tools -%}

      {%- if project.external_url -%}
        {%- assign project_url = project.external_url -%}
        {%- assign external    = true -%}
      {%- else -%}
        {%- assign project_url = project.url | relative_url -%}
        {%- assign external    = false -%}
      {%- endif -%}

      {% include projects/project-card.html %}

    {% endif %}
  {% endfor %}

</div>

<!-- 
{% include about/timeline.html source=site.data.industry-timeline %}
-->
