---
layout: page
permalink: /publications/
title: publications
description: publications by categories in reversed chronological order.
nav: true
nav_order: 1
---
<!-- _pages/publications.md -->
<div class="publications">

{% bibliography -f {{ site.scholar.bibliography }} %}
<!-- submissions -->
<ol class="bibliography">

 <li>
  <div class="row">
   <div id="submission1" class="col-sm-8">
    <div class="title">Self-Interactive Attention Networks for CTR Prediction</div>
    <div class="author">Dongjun Lee, &nbsp;Hyunsung Lee,&nbsp;and&nbsp;Jaewang Kim</div>
    <div class="periodical"><em>The 24nd International Symposium on Advanced Intelligent Systems</em>", 2023" (accepted)</div>
    <div class="periodical"> </div>
   </div>
  </div>
 </li>
 <li>
  <div class="row">
   <div id="submission2" class="col-sm-8">
    <div class="title">Elevating CTR Prediction: Field Interaction, Global Context Integration, and High-Order Representations</div>
    <div class="author">Sojeong Kim, &nbsp;Dongjun Lee,&nbsp;and&nbsp;Jaewang Kim</div>
    <div class="periodical">underreview</div>
    <div class="periodical"> </div>
   </div>
  </div>
 </li>
 <li>
  <div class="row">
   <div id="submission2" class="col-sm-8">
    <div class="title">Debiasing via Amplifying Bias with Latent Diffusion Model</div>
    <div class="author">" Dongguen Ko, &nbsp;Dongjun Lee, &nbsp;Namjun Park, and " 
     <span class="more-authors" title="click to view 2 more authors" onclick=" var element=$(this); element.attr('title', ''); var more_authors_text=element.text() == '2 more authors' ? 'Wonkyeong Shim, Jaekwang Kim' : '2 more authors'; var cursorPosition=0; var textAdder=setInterval(function(){ element.text(more_authors_text.substring(0, cursorPosition + 1)); if (++cursorPosition == more_authors_text.length){ clearInterval(textAdder); } }, '10'); ">2 more authors</span>
    </div>
    <div class="periodical">underreview</div>
    <div class="periodical"> </div>
   </div>
  </div>
 </li>

</ol>

</div>