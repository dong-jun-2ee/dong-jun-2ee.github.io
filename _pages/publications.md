---
layout: page
permalink: /publications/
title: publications
description: This page exclusively catalogs papers that have been submitted to or officially published in the proceedings of the International Conference.
nav: true
nav_order: 1
---
<!-- _pages/publications.md -->
<h1 class="post-title"><a href="/assets/pdf/portfolio.pdf" target="_blank" rel="noopener noreferrer" class="float-right"><i class="fas fa-file-pdf"></i></a></h1>

<div class="publications">

{% bibliography -f {{ site.scholar.bibliography }} %}
<!-- submissions -->
<ol class="bibliography">

 <li>
  <div class="row">
   <div class="col-sm-2 preview">
    <figure> <picture> <source class="responsive-img-srcset" media="(max-width: 480px)" srcset="/assets/img/publication_preview/SIAN-480.webp"> <source class="responsive-img-srcset" media="(max-width: 800px)" srcset="/assets/img/publication_preview/SIAN-800.webp"> <source class="responsive-img-srcset" media="(max-width: 1400px)" srcset="/assets/img/publication_preview/SIAN-1400.webp"> <img src="/assets/img/publication_preview/SIAN.png" class="preview z-depth-1 rounded" width="auto" height="auto" alt="SIAN.png" onerror="this.onerror=null; $('.responsive-img-srcset').remove();"> </picture></figure>
   </div>
   <div id="submission1" class="col-sm-8">
    <div class="title">Self-Interactive Attention Netwroks via Factorization Machinces for Click-Through Rate Prediction</div>
    <div class="author">Dongjun Lee, &nbsp;Hyunsung Lee,&nbsp;and&nbsp;Jaewang Kim</div>
    <div class="periodical"><em>In Proceedings of the 24nd International Symposium on Advanced Intelligent Systems</em>, 2023 (accepted)</div>
    <div class="periodical"> </div>
   </div>
  </div>
 </li>
 <li>
  <div class="row">
  <div class="col-sm-2 preview">
    <figure> <picture> <source class="responsive-img-srcset" media="(max-width: 480px)" srcset="/assets/img/publication_preview/SAC-480.webp"> <source class="responsive-img-srcset" media="(max-width: 800px)" srcset="/assets/img/publication_preview/SAC-800.webp"> <source class="responsive-img-srcset" media="(max-width: 1400px)" srcset="/assets/img/publication_preview/SAC-1400.webp"> <img src="/assets/img/publication_preview/SAC.png" class="preview z-depth-1 rounded" width="auto" height="auto" alt="SAC.png" onerror="this.onerror=null; $('.responsive-img-srcset').remove();"> </picture></figure>
   </div>
   <div id="submission2" class="col-sm-8">
    <div class="title">Elevating CTR Prediction: Field Interaction, Global Context Integration, and High-Order Representations</div>
    <div class="author">Sojeong Kim, &nbsp;Dongjun Lee,&nbsp;and&nbsp;Jaewang Kim</div>
    <div class="periodical">Accepted</div>
    <div class="periodical"> </div>
   </div>
  </div>
 </li>
 <li>
  <div class="row">
  <div class="col-sm-2 preview">
    <figure> <picture> <source class="responsive-img-srcset" media="(max-width: 480px)" srcset="/assets/img/publication_preview/DiffBias-480.webp"> <source class="responsive-img-srcset" media="(max-width: 800px)" srcset="/assets/img/publication_preview/DiffBias-800.webp"> <source class="responsive-img-srcset" media="(max-width: 1400px)" srcset="/assets/img/publication_preview/DiffBias-1400.webp"> <img src="/assets/img/publication_preview/DiffBias.png" class="preview z-depth-1 rounded" width="auto" height="auto" alt="DiffBias.png" onerror="this.onerror=null; $('.responsive-img-srcset').remove();"> </picture></figure>
   </div>
   <div id="submission2" class="col-sm-8">
    <div class="title">Debiasing via Amplifying Bias with Latent Diffusion Model</div>
    <div class="author">Dongguen Ko, &nbsp;Dongjun Lee, &nbsp;Namjun Park, and 
     <span class="more-authors" title="click to view 2 more authors" onclick=" var element=$(this); element.attr('title', ''); var more_authors_text=element.text() == '2 more authors' ? 'Wonkyeong Shim, Jaekwang Kim' : '2 more authors'; var cursorPosition=0; var textAdder=setInterval(function(){ element.text(more_authors_text.substring(0, cursorPosition + 1)); if (++cursorPosition == more_authors_text.length){ clearInterval(textAdder); } }, '10'); ">2 more authors</span>
    </div>
    <div class="periodical">underreview</div>
    <div class="periodical"> </div>
   </div>
  </div>
 </li>

</ol>

</div>