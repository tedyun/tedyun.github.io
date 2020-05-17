---
layout: post
title: "Adding custom javascript"
vis: true
---

# We test adding custom HTML and javascript here.

Text.

<div id="demo1">
  <h3>
    Click Me!
  </h3>
</div>

<link href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css" rel="stylesheet">

<script>
  $("#demo1").click(function() {
    $(this).addClass("rubberBand animated");

    setTimeout( function(){
      $("#demo1").removeClass("rubberBand animated");
    }, 1000);
  });
</script>
