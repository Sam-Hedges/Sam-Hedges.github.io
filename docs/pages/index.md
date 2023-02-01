---
layout: default
permalink: /
---

{% include landing.html %}

<div class="col mt-4">
  <div class="timeline-body bg-themed">
  <!-- TO CHANGE THE TIMELINE EDIT site.data.ENTER_YML_FILE_HERE -->
    {% for item in site.data.industry-timeline %} 
      <div class="timeline-item">
        <div class="content">
          <h2>{{ item.title }}</h2>
          <h6 class="date">{{ item.from }} — {{ item.to }}</h6>
          <p>{{ item.description }}</p>
        </div>
      </div>
    {% endfor %}
  </div>
</div>

{% include projects/index.html %}