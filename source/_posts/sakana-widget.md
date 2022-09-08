---
title: sakana widget
date: 2022-09-08 22:23:48
updated: 2022-09-08 22:23:48
tags:
    - 分享
categories: 
    - 小玩具
---

放一只sakana在这里

<!-- https://cdn.jsdelivr.net/npm/sakana-widget@2.3.0/lib/sakana.min.js -->
<!-- https://cdnjs.cloudflare.com/ajax/libs/sakana-widget/2.3.0/sakana.min.js -->
<div id="sakana-widget"></div>
<script>
  function initSakanaWidget() {
    new SakanaWidget().mount('#sakana-widget');
  }
</script>
<script
  async
  onload="initSakanaWidget()"
  src="https://cdn.jsdelivr.net/npm/sakana-widget@2.3.0/lib/sakana.min.js"
></script>
