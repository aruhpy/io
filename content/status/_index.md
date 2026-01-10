---
title: "진행상황"
bodyClass: page-status
draft: false
---
<div class="sheet-embed">
  <div class="sheet-embed-head">
    <span class="sheet-sub">※ 모바일은 좌우 스크롤이 필요할 수 있어요</span>
  </div>

  <div class="status-table-wrap" id="statusTable">
    <div class="status-loading">불러오는 중…</div>
  </div>
</div>

<script>
(() => {
  const SHEET_ID = "1Ree6stnlpqoOOHAMj_HUnaii78oR6ZqR-JM-VdoZQbw";
  const GID = "998125620";
  const QUERY = "select A,B,C,D,E,F where C is not null"; // C=닉네임

  const mount = document.getElementById("statusTable");

  function showError(title, detail){
    mount.innerHTML =
      '<div class="status-error">' +
      title +
      (detail ? '<br><small style="opacity:.85; word-break:break-word;">' + detail + '</small>' : '') +
      '</div>';
  }

  // ✅ 진행상황(=E열, index 4) 텍스트 → 칩 클래스 매핑
  function statusClass(text){
    const v = (text || "").trim();

    // 자주 쓰는 상태들: 너 시트 값에 맞춰 여기만 더 추가하면 됨
    if (/결제\s*완료/.test(v)) return "is-paid";
    if (/결제\s*대기|입금\s*대기/.test(v)) return "is-wait-pay";
    if (/작업\s*중|진행\s*중/.test(v)) return "is-working";
    if (/완료|전달\s*완료/.test(v)) return "is-done";
    if (/대기/.test(v)) return "is-wait";
    if (/취소|환불/.test(v)) return "is-cancel";

    return "is-etc";
  }

  function renderTable(data){
    if (!data || data.status !== "ok" || !data.table) {
      const msg =
        (data && data.errors && data.errors[0] &&
          (data.errors[0].detailed_message || data.errors[0].message))
          ? (data.errors[0].detailed_message || data.errors[0].message)
          : "권한/게시 설정 또는 네트워크를 확인해주세요.";
      showError("진행상황을 불러오지 못했어요.", msg);
      return;
    }

    const table = data.table;

    // ✅ headers=1을 쓰면 cols.label이 첫 행(신청 날짜….)로 채워짐
    const cols = table.cols.map((c, i) =>
      (c.label && c.label.trim()) ? c.label : ("COL" + (i + 1))
    );

    const rows = (table.rows || []).map(r =>
      (r.c || []).map(cell => (cell && cell.v != null) ? String(cell.v) : "")
    );

    // ✅ 닉네임(C열 index 2) 비어있는 행 제거
    const filtered = rows.filter(r => (r[2] || "").trim() !== "");

    const wrap = document.createElement("div");
    wrap.className = "status-table-inner";

    const t = document.createElement("table");
    t.className = "status-table";

    const thead = document.createElement("thead");
    const trh = document.createElement("tr");
   const thNo = document.createElement("th");
thNo.textContent = "";
thNo.className = "col-no";
trh.appendChild(thNo);

// 기존 헤더
cols.forEach(h => {
  const th = document.createElement("th");
  th.textContent = h;
  trh.appendChild(th);
});
    thead.appendChild(trh);

  const tbody = document.createElement("tbody");

filtered.forEach(row => {
  const tr = document.createElement("tr");
// ✅ 순번(1부터)
const tdNo = document.createElement("td");
tdNo.className = "col-no";
tdNo.textContent = String(tbody.childElementCount + 1);
tr.appendChild(tdNo);

  cols.forEach((_, i) => {
    const td = document.createElement("td");
    const val = row[i] ?? "";
    // ✅ 신청타입(D열= index 3) 전체 보기용
    if (i === 3) td.title = String(val || "");
    // 플랫폼(B열= index 1)
    if (i === 1) {
      const v = String(val).trim();
      const chip = document.createElement("span");
      chip.className =
        "platform-chip " +
        (v.includes("크레페") ? "is-crepe" : (v.includes("카페") ? "is-cafe" : "is-etc"));
      chip.textContent = v || "-";
      td.appendChild(chip);
    }
    // 진행상황(E열= index 4)
    else if (i === 4) {
      const span = document.createElement("span");
      span.className = "status-chip " + statusClass(val);
      span.textContent = String(val);
      td.appendChild(span);
    }
    // 마감날짜(F열= index 5) : '빠마'만 강조
    else if (i === 5) {
      const v = String(val || "");
      const key = "빠마";

      if (v.includes(key)) {
        const parts = v.split(key);
        td.appendChild(document.createTextNode(parts[0] || ""));

        const hi = document.createElement("span");
        hi.className = "deadline-bamma";
        hi.textContent = key;
        td.appendChild(hi);

        td.appendChild(document.createTextNode(parts.slice(1).join(key) || ""));
      } else {
        td.textContent = v;
      }
    }
    else {
      td.textContent = String(val);
    }

    tr.appendChild(td);
  });

  tbody.appendChild(tr);
});



    t.appendChild(thead);
    t.appendChild(tbody);
    wrap.appendChild(t);

    mount.innerHTML = "";
    mount.appendChild(wrap);
// ✅ 모바일: 신청타입(4열) 탭하면 전체를 토스트로 표시
const toast = document.createElement("div");
toast.className = "status-toast";
toast.setAttribute("aria-live", "polite");
mount.appendChild(toast);

function showToast(msg){
  toast.textContent = msg;
  toast.classList.add("is-show");
  clearTimeout(showToast._t);
  showToast._t = setTimeout(() => toast.classList.remove("is-show"), 1700);
}

// 테이블 클릭 이벤트(신청타입 열만 반응)
t.addEventListener("click", (e) => {
  const td = e.target.closest("td");
  if (!td) return;

  if (td.cellIndex !== 3) return; // D열(4번째)=신청타입

  const full = (td.getAttribute("title") || td.textContent || "").trim();
  if (!full) return;

  showToast(full);
});

  }

  function loadGVIZ(){
    if (mount.dataset.loaded === "1") return;
    mount.dataset.loaded = "1";

    const cbName = "__gvizCallback_" + Date.now() + "_" + Math.floor(Math.random() * 1e6);

    window[cbName] = (data) => {
      try { renderTable(data); }
      catch(e){ showError("표를 만드는 중 오류가 발생했어요.", String(e)); console.error(e); }
      finally { try { delete window[cbName]; } catch(_){} }
    };

    // ✅ 핵심: headers=1 추가 → COL1 문제 해결
    const src =
      "https://docs.google.com/spreadsheets/d/" + encodeURIComponent(SHEET_ID) +
      "/gviz/tq?gid=" + encodeURIComponent(GID) +
      "&headers=1" +
      "&tq=" + encodeURIComponent(QUERY) +
      "&tqx=" + encodeURIComponent("out:json;responseHandler:" + cbName) +
      "&v=" + Date.now();

    const s = document.createElement("script");
    s.src = src;
    s.async = true;
    s.onerror = () => {
      showError("진행상황을 불러오지 못했어요.", "스크립트 로드 실패(권한/게시 또는 네트워크)");
      try { delete window[cbName]; } catch(_) {}
    };
    document.head.appendChild(s);
  }

  // ✅ 너가 이미 적용한 “보일 때만 로드” 유지
  if ("IntersectionObserver" in window) {
    const io = new IntersectionObserver((entries) => {
      if (entries.some(e => e.isIntersecting)) {
        io.disconnect();
        loadGVIZ();
      }
    }, { rootMargin: "200px" });
    io.observe(mount);
  } else {
    loadGVIZ();
  }
})();
</script>

