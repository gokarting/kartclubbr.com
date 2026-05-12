---
layout: default
title: Calendar
subtitle: Race dates and circuits
---

{% assign now_ts = 'now' | date: '%s' | plus: 0 %}
{% assign next_race = nil %}
{% for race in site.data.race_calendar %}
  {% assign race_ts = race.date | date: '%s' | plus: 0 %}
  {% if race_ts >= now_ts %}
    {% assign next_race = race %}
    {% break %}
  {% endif %}
{% endfor %}

<div class="kc-page-shell">
<section class="kc-hero-panel kc-page-hero">
  <p class="kc-eyebrow">Race Calendar</p>
  <h2>2026 Season Schedule</h2>
  <p>Official round dates and circuits for the season.</p>
</section>

<section class="kc-table-wrap" aria-label="Race calendar table">
  <table class="kc-calendar-table">
    <thead>
      <tr>
        <th>Date</th>
        <th>Circuit</th>
      </tr>
    </thead>
    <tbody>
      {% for race in site.data.race_calendar %}
      <tr{% if next_race and race.date == next_race.date %} class="kc-next-row"{% endif %}>
        <td>{{ race.date_display }}</td>
        <td>{{ race.circuit }}</td>
      </tr>
      {% endfor %}
    </tbody>
  </table>
</section>
</div>
