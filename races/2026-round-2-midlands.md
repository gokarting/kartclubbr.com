---
layout: default
title: Round 2 Details
subtitle: Midlands - 14/06/2026
share-title: Round 2 Midlands | KARTCLUBBR
share-description: Driver position evolution and race details for Round 2 at Midlands.
comments: false
---

<div class="kc-page-shell">
<section class="kc-hero-panel kc-page-hero">
  <p class="kc-eyebrow">Round 2</p>
  <h2>Midlands - 14/06/2026</h2>
  <p>Race position evolution across laps. Click a driver in the legend to show or hide their line.</p>
  <a class="kc-btn kc-btn-ghost" href="{{ '/calendar' | relative_url }}">Back to Calendar</a>
</section>

<section class="kc-hero-panel" style="padding: 1.25rem;">
  <h3 style="margin-bottom: 0.5rem;">Race Position Evolution</h3>
  <p style="margin-bottom: 1rem; opacity: 0.8;">Hover over the chart to inspect each lap. Y axis is race position.</p>

  <div id="legend" style="display:flex;flex-wrap:wrap;gap:6px 14px;margin-bottom:1rem;"></div>

  <div style="position:relative;height:560px;">
    <canvas id="raceChart" role="img" aria-label="Line chart showing each driver's position from lap 0 to lap 36.">Race position evolution for 19 drivers across 36 laps.</canvas>
    <div id="tooltip" style="position:absolute;pointer-events:none;background:#fff;border:1px solid rgba(0,0,0,0.18);border-radius:8px;padding:8px 12px;font-size:12px;color:#1a1a1a;display:none;z-index:10;min-width:150px;box-shadow:0 2px 8px rgba(0,0,0,0.08);"></div>
  </div>
</section>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<script>
const drivers = [
  'Bruno Vilassa','Lorcan Bellew','Diego Pompeu','Ion Marcu','Lucas Cavalcan',
  'VinnY Dias','Ronan Silva','Rodrigo Camach','Ronan Kierans','Douglas Robido',
  'Everton Costa','Bart Felipe','Felipe de Oliv','Eduardo Arenha','Rafael Thibes',
  'Ken O Sullivan','Wallace Teixei','Guilherm Mello','Priscilla Libe'
];

const maxLap = 36;

const positionData = {
  'Bruno Vilassa':  [6,3,3,4,4,3,3,3,3,2,2,3,2,1,1,1,1,1,1,1,1,1,1,1,1,1,1,2,2,2,1,1,1,1,1,1,1],
  'Lorcan Bellew':  [5,6,6,5,2,2,2,1,1,1,1,1,1,3,3,3,3,2,2,2,2,2,2,2,2,2,2,1,1,1,2,2,2,2,2,2,2],
  'Diego Pompeu':   [8,5,5,3,1,1,1,2,2,3,3,2,3,2,2,2,2,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3,3],
  'Ion Marcu':      [2,4,4,6,5,5,5,6,6,6,5,5,5,5,6,6,5,5,5,5,5,5,4,4,4,4,4,4,4,4,4,4,4,4,4,4,4],
  'Lucas Cavalcan': [17,10,10,9,9,8,8,7,7,7,6,6,6,6,4,4,4,4,4,4,4,4,5,5,5,5,5,5,5,5,5,5,5,5,5,5,5],
  'VinnY Dias':     [1,1,1,2,6,6,6,5,5,5,7,7,7,7,7,7,7,7,7,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6],
  'Ronan Silva':    [15,13,12,12,12,11,11,11,11,11,10,10,10,9,9,9,9,9,9,9,8,8,8,8,8,8,8,8,8,7,7,8,7,7,7,7,7],
  'Rodrigo Camach': [9,8,8,7,7,7,7,8,8,8,8,8,8,8,8,8,8,8,8,8,7,7,7,7,7,7,7,7,7,8,8,7,8,8,8,8,8],
  'Ronan Kierans':  [10,11,11,11,16,16,16,15,15,15,14,14,13,13,13,13,13,13,13,13,10,10,12,10,10,9,9,9,9,9,9,9,9,9,9,9,null],
  'Douglas Robido': [14,15,13,13,11,12,12,12,12,12,12,12,12,12,12,12,12,12,12,12,11,11,10,12,12,12,11,11,11,11,10,10,10,10,10,10,null],
  'Everton Costa':  [12,9,9,10,10,10,10,10,10,10,11,11,11,10,10,10,10,10,10,10,9,9,9,9,9,10,10,10,10,10,11,11,11,11,11,11,null],
  'Bart Felipe':    [16,17,16,15,14,14,14,14,13,13,13,13,14,14,14,14,14,14,14,14,13,13,13,13,13,13,13,13,13,13,12,12,12,12,12,12,null],
  'Felipe de Oliv': [7,7,7,8,8,9,9,9,9,9,9,9,9,11,11,11,11,11,11,11,12,12,11,11,11,11,12,12,12,12,13,13,13,13,13,13,null],
  'Eduardo Arenha': [4,12,14,14,13,13,13,13,14,14,15,15,15,15,15,15,15,15,15,15,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,14,null],
  'Rafael Thibes':  [18,16,17,17,17,17,17,17,17,17,17,17,17,17,17,17,17,17,17,17,16,15,15,15,16,15,15,15,15,16,16,16,16,16,16,15,null],
  'Ken O Sullivan': [13,14,15,16,15,15,15,16,16,16,16,16,16,16,16,16,16,16,16,16,15,16,16,16,15,16,16,16,16,15,15,15,15,15,15,16,null],
  'Wallace Teixei': [11,18,18,18,18,18,18,18,18,18,18,18,18,18,18,18,18,18,18,18,17,17,17,17,17,17,17,17,17,17,17,17,17,17,17,17,null],
  'Guilherm Mello': [3,2,2,1,3,4,4,4,4,4,4,4,4,4,5,5,6,6,6,7,19,19,19,19,19,19,19,19,19,19,18,18,null,null,null,null,null],
  'Priscilla Libe': [19,19,19,19,19,19,19,19,19,19,19,19,19,19,19,19,19,19,19,19,18,18,18,18,18,18,18,18,18,18,19,19,null,null,null,null,null]
};

const palette = [
  '#378ADD','#D85A30','#1D9E75','#D4537E','#BA7517','#534AB7','#639922','#E24B4A',
  '#0F6E56','#993556','#3B6D11','#185FA5','#854F0B','#A32D2D','#0C447C','#3C3489',
  '#7F77DD','#63220A','#888780'
];

const labels = Array.from({length: maxLap + 1}, (_, i) => i);
const hidden = new Set();

const datasets = drivers.map((name, i) => ({
  label: name,
  data: labels.map(lap => positionData[name][lap] ?? null),
  borderColor: palette[i],
  backgroundColor: palette[i],
  borderWidth: 1.8,
  pointRadius: 0,
  pointHoverRadius: 5,
  tension: 0,
  spanGaps: false
}));

const chartContainer = document.getElementById('raceChart').parentElement;
const canvas = document.getElementById('raceChart');

const chart = new Chart(canvas, {
  type: 'line',
  data: { labels, datasets },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    interaction: { mode: 'index', intersect: false },
    plugins: {
      legend: { display: false },
      tooltip: { enabled: false }
    },
    scales: {
      x: {
        title: { display: true, text: 'Lap', font: { size: 12 }, color: '#888' },
        ticks: { font: { size: 11 }, color: '#888', stepSize: 2 },
        grid: { color: 'rgba(128,128,128,0.1)' }
      },
      y: {
        reverse: true,
        min: 0,
        max: 20,
        ticks: {
          stepSize: 1,
          font: { size: 11 },
          color: '#888',
          callback: v => (Number.isInteger(v) && v > 0 && v < 20) ? 'P' + v : ''
        },
        title: { display: true, text: 'Position', font: { size: 12 }, color: '#888' },
        grid: { color: 'rgba(128,128,128,0.1)' }
      }
    }
  }
});

const legend = document.getElementById('legend');
drivers.forEach((name, i) => {
  const item = document.createElement('button');
  item.type = 'button';
  item.style.display = 'inline-flex';
  item.style.alignItems = 'center';
  item.style.gap = '5px';
  item.style.fontSize = '12px';
  item.style.color = '#555';
  item.style.border = 'none';
  item.style.background = 'transparent';
  item.style.cursor = 'pointer';
  item.style.padding = '2px 0';
  item.innerHTML = '<span style="width:20px;height:3px;border-radius:2px;display:inline-block;background:' + palette[i] + '"></span>' + name;

  item.addEventListener('click', () => {
    const meta = chart.getDatasetMeta(i);
    if (hidden.has(i)) {
      hidden.delete(i);
      meta.hidden = false;
      item.style.opacity = '1';
    } else {
      hidden.add(i);
      meta.hidden = true;
      item.style.opacity = '0.3';
    }
    chart.update();
  });

  legend.appendChild(item);
});

const tooltip = document.getElementById('tooltip');
canvas.addEventListener('mousemove', e => {
  const rect = canvas.getBoundingClientRect();
  const x = e.clientX - rect.left;
  const points = chart.getElementsAtEventForMode(e, 'index', { intersect: false }, true);

  if (!points.length) {
    tooltip.style.display = 'none';
    return;
  }

  const lap = points[0].index;
  const atLap = drivers
    .map((name, i) => {
      const pos = positionData[name][lap];
      return pos != null ? { name, pos, color: palette[i], isHidden: hidden.has(i) } : null;
    })
    .filter(Boolean)
    .sort((a, b) => a.pos - b.pos);

  let html = '<div style="font-size:11px;color:#888;margin-bottom:4px;">Lap ' + lap + '</div>';
  atLap.forEach(driver => {
    if (driver.isHidden) return;
    html += '<div style="display:flex;align-items:center;gap:6px;padding:1px 0;">'
      + '<span style="width:8px;height:8px;border-radius:50%;display:inline-block;background:' + driver.color + '"></span>'
      + '<span style="min-width:24px;font-weight:500;">P' + driver.pos + '</span>'
      + '<span style="color:#666;">' + driver.name + '</span>'
      + '</div>';
  });

  tooltip.innerHTML = html;
  tooltip.style.display = 'block';

  const tooltipWidth = tooltip.offsetWidth;
  const containerWidth = chartContainer.offsetWidth;
  let left = x + 14;
  if (left + tooltipWidth > containerWidth - 10) left = x - tooltipWidth - 14;

  tooltip.style.left = left + 'px';
  tooltip.style.top = '10px';
});

canvas.addEventListener('mouseleave', () => {
  tooltip.style.display = 'none';
});
</script>
