---
layout: default
---

<body>
  <div class="index-wrapper">
    <div class="aside">
      <div class="info-card">
        <h1>追忆似水年华</h1>
        <a href="https://www.douban.com/people/83097413/" target="_blank"><img src="https://www.douban.com/favicon.ico" alt="" width="22"/></a>
        <a href="https://stackexchange.com/users/3904305/joehu?tab=accounts" target="_blank"><img src="https://sstatic.net/stackexchange/img/favicon.ico" alt="" width="22"/></a>
        <a href="http://steamcommunity.com/id/huxingyu/" target="_blank"><img src="http://store.steampowered.com/favicon.ico" alt="" width="22"/></a>
        <a href="http://codeforces.com/profile/huxingyu1996" target="_blank"><img src="http://codeforces.com/favicon.ico" alt="" width="22"/></a>
        <a href="https://github.com/Huxingyu" target="_blank"><img src="https://github.com/favicon.ico" alt="" width="22"/></a>
        <a href="http://dblp.uni-trier.de/" target="_blank"><img src="http://dblp.uni-trier.de/img/favicon.ico" alt="" width="22"/></a>
      </div>
      <div id="particles-js"></div>
    </div>

    <div class="index-content">
      <ul class="artical-list">
        {% for post in site.categories.blog %}
        <li>
          <a href="{{ post.url }}" class="title">{{ post.title }}</a>
          <div class="title-desc">{{ post.description }}</div>
        </li>
        {% endfor %}
      </ul>
    </div>
  </div>
  
  <script src="http://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js"></script>
  <script>
    particlesJS("particles-js", {"particles":{"number":{"value":160,"density":{"enable":true,"value_area":800}},"color":{"value":"#ffffff"},"shape":{"type":"circle","stroke":{"width":0,"color":"#000000"},"polygon":{"nb_sides":5},"image":{"src":"img/github.svg","width":100,"height":100}},"opacity":{"value":1,"random":true,"anim":{"enable":true,"speed":1,"opacity_min":0,"sync":false}},"size":{"value":3,"random":true,"anim":{"enable":false,"speed":4,"size_min":0.3,"sync":false}},"line_linked":{"enable":true,"distance":150,"color":"#ffffff","opacity":0.4,"width":1},"move":{"enable":true,"speed":1,"direction":"none","random":true,"straight":false,"out_mode":"out","bounce":false,"attract":{"enable":false,"rotateX":600,"rotateY":600}}},"interactivity":{"detect_on":"canvas","events":{"onhover":{"enable":true,"mode":"bubble"},"onclick":{"enable":true,"mode":"repulse"},"resize":true},"modes":{"grab":{"distance":400,"line_linked":{"opacity":1}},"bubble":{"distance":250,"size":0,"duration":2,"opacity":0,"speed":3},"repulse":{"distance":400,"duration":0.4},"push":{"particles_nb":4},"remove":{"particles_nb":2}}},"retina_detect":true})
</script>

</body>
