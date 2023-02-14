---
title: "Publications"
layout: gridlay
excerpt: "Publications."
sitemap: true
permalink: /publications/
description:
nav: true
toc: true
---


<!-- ## Featured -->
<br>
<div style="text-align:center">
<a href="https://onlinelibrary.wiley.com/doi/full/10.1002/lpor.202100008" target="_blank"><img src="{{site.baseurl}}/images/research/lpor202106.jpg"  style="height:200px; border-radius: 0%; box-shadow: 0px 0px 0px #ffffff00;"></a>
<a href="https://www.osapublishing.org/prj/home.cfm" target="_blank"><img src="{{site.baseurl}}/images/research/prj.jpg"  style="height:200px; border-radius: 0%;%"></a>
<a href="https://www.journals.elsevier.com/optics-and-lasers-in-engineering" target="_blank"><img src="{{site.baseurl}}/images/research/ole.jpg"  style="height:200px; border-radius: 0%;%"></a>
<a href="https://www.osapublishing.org/oe/home.cfm" target="_blank"><img src="{{site.baseurl}}/images/research/oe.jpg"  style="height:200px; border-radius: 0%;%"></a>
</div>
<br>

<!-- Nav -->
<ul class="nav nav-tabs" style="width:100%; margin: 0 auto;">
    <li class="active">
    <a data-toggle="tab"><button id="btn_bytype" onclick="ShowOrHide('bytype')">By Type</button></a>
    </li>
    <li class="">
    <a data-toggle="tab"><button id="btn_byyear" onclick="ShowOrHide('byyear')">By Year</button> </a>
    </li>
    <li class="">
    <a data-toggle="tab"><button id="btn_bydiscip" onclick="ShowOrHide('bydiscip')">By Discipline</button> </a>
    </li>
    <li class="">
    <a data-toggle="tab"><button id="btn_bytalk" onclick="ShowOrHide('bytalk')">Talks</button></a>
    </li>
    <li class="">
    <a data-toggle="tab"><button id="btn_inpress" onclick="ShowOrHide('inpress')">In Progress</button></a>
    </li>
    <li class="">
    <a data-toggle="tab" href="https://scholar.google.com/citations?hl=en&user=adQED6IAAAAJ&view_op=list_works&authuser=1&sortby=pubdate"><button onclick="window.location.href='https://scholar.google.com/citations?hl=en&user=adQED6IAAAAJ&view_op=list_works&authuser=1&sortby=pubdate';">Google Scholar</button></a>
    </li>
</ul>

<br>

<!-- <script>
let thisyear = new Date().getFullYear();
</script> -->

{% assign years = (2009..2023) | reverse %}
<script>var byyeartext="year";var bytypetext="type";</script>


<!-- Display by year -->
<div id="byyear" style="display:none;">

{% for y in years %}
  <h3 id="{{y}}">{{y}}</h3>
  {% bibliography -f journal -f conf -q @*[year={{y}}]* %}
{% endfor %}

</div>

<!-- Display by Disciplinary -->
<div id="bydiscip" style="display:none;">

## Cross-disciplinary
{% bibliography -f journal -f conf -q @*[disciplinary=cross] %}

## Computer science
{% bibliography -f journal -f conf -q @*[disciplinary=computer] %}

## Optics
{% bibliography -f journal -f conf -q @*[disciplinary=optics] %}


</div>


<!-- In Progress -->
<div id="inpress" style="display:none;">

{% bibliography -f journal -q @misc %}

</div>

<!-- Display by type -->
<div id="bytype" style="display:block;">

## Journal papers
{% bibliography -f journal -q @article %}

## Conference
{% bibliography -f conf -q @conference %}

## Patent
{% bibliography -f others -q @patent %}

</div>


<!-- talks -->
<div id="bytalk" style="display:none;">

### Selected talks

{% bibliography -f conf -q @talk || @conference[key=Chen2021DH || key=Chen2020DH] %}

</div>



