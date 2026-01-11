---
title: "타입 정리"
bodyClass: "is-page page-type-index"
---


<link rel="stylesheet" href="type.css">

<!-- ✅ 상단 칩 분류(타입정리 전용) -->
<div class="type-chipbar" role="tablist" aria-label="타입 분류">
  <button type="button" class="type-chip is-active" data-filter="all" role="tab" aria-selected="true">전체</button>
  <button type="button" class="type-chip" data-filter="sd" role="tab" aria-selected="false">SD</button>
  <button type="button" class="type-chip" data-filter="dot" role="tab" aria-selected="false">도트</button>
  <button type="button" class="type-chip" data-filter="line" role="tab" aria-selected="false">마감선</button>
</div>

<!-- ✅ data-filter 값에 따라 CSS가 카드 숨김 처리 -->
<div class="type-grid" data-filter="all">


<!-- 겨우르 = SD -->
<a class="type-card" href="wi/" data-cats="sd">
  <div class="type-thumb">
    <img src="thumb_wi.webp" alt="겨우르 타입" loading="lazy">
  </div>
  <div class="type-name">겨우르 타입</div>
</a>

<!-- 멜로 텐시 = SD + line -->
<a class="type-card" href="me/" data-cats="sd line">
  <div class="type-thumb">
    <img src="thumb_me.webp" alt="멜로 텐시 타입" loading="lazy">
  </div>
  <div class="type-name">멜로 텐시 타입</div>
</a>

  <!-- 보들 = SD -->
  <a class="type-card" href="bo/" data-cats="sd">
    <div class="type-thumb">
      <img src="thumb_bo.webp" alt="보들 타입" loading="lazy">
    </div>
    <div class="type-name">보들 타입</div>
  </a>

  <!-- 핫듀 = 마감선 -->
  <a class="type-card" href="ha/" data-cats="line">
    <div class="type-thumb">
      <img src="thumb_ha.webp" alt="핫듀핫듀 타입" loading="lazy">
    </div>
    <div class="type-name">핫듀핫듀 타입</div>
  </a>
 
  <!-- 패치워크 = 도트 -->
  <a class="type-card" href="pa/" data-cats="dot">
    <div class="type-thumb">
      <img src="thumb_pa.webp" alt="패치워크 타입" loading="lazy">
    </div>
    <div class="type-name">패치워크 타입</div>
  </a>

 <!-- 무지개 = 마감선 + 도트 -->
  <a class="type-card" href="mu/" data-cats="line dot">
    <div class="type-thumb">
      <img src="thumb_mu.webp" alt="무지개구름 타입" loading="lazy">
    </div>
    <div class="type-name">무지개구름 타입</div>
  </a>

 <!-- 몽글 = 마감선 -->
  <a class="type-card" href="mong/" data-cats="line">
    <div class="type-thumb">
      <img src="thumb_mong.webp" alt="몽글망글 타입" loading="lazy">
    </div>
    <div class="type-name">몽글망글 타입</div>
  </a>


</div>

<script>
(function(){
  var grid = document.querySelector('body.page-type-index .type-grid');
  var chips = document.querySelectorAll('body.page-type-index .type-chip');
  if(!grid || !chips.length) return;

  function setActive(filter){
    grid.setAttribute('data-filter', filter);

    chips.forEach(function(btn){
      var isOn = btn.getAttribute('data-filter') === filter;
      btn.classList.toggle('is-active', isOn);
      btn.setAttribute('aria-selected', isOn ? 'true' : 'false');
    });

    // URL에 cat 파라미터로 저장(새로고침/공유용) - 프로젝트페이지 안전
    var url = new URL(window.location.href);
    if(filter === 'all'){
      url.searchParams.delete('cat');
    }else{
      url.searchParams.set('cat', filter);
    }
    window.history.replaceState({}, '', url.toString());
  }

  // 초기: URL ?cat=sd/dot/line 있으면 적용
  var params = new URLSearchParams(window.location.search);
  var cat = params.get('cat');
  if(cat === 'sd' || cat === 'dot' || cat === 'line'){
    setActive(cat);
  }else{
    setActive('all');
  }

  chips.forEach(function(btn){
    btn.addEventListener('click', function(){
      var filter = btn.getAttribute('data-filter') || 'all';
      setActive(filter);
    });
  });
})();
</script>
