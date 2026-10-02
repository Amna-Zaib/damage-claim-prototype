index.html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ClaimFlow — Damage Claim Resolution Prototype</title>
<style>
  :root{
    --bg:#0f1115; --panel:#161a21; --panel2:#1d222b; --border:#2a3140;
    --text:#e8ecf1; --muted:#8a95a6; --accent:#4f8cff; --accent2:#2c6fe0;
    --green:#36c58c; --yellow:#e0b84f; --red:#e25c5c; --purple:#9b7bea;
  }
  *{box-sizing:border-box;}
  body{
    margin:0; font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial,sans-serif;
    background:var(--bg); color:var(--text); min-height:100vh;
  }
  header{
    padding:18px 20px; border-bottom:1px solid var(--border);
    display:flex; align-items:center; justify-content:space-between; flex-wrap:wrap; gap:10px;
  }
  header h1{font-size:18px; margin:0; display:flex; align-items:center; gap:8px;}
  header h1 .dot{width:10px;height:10px;border-radius:50%;background:var(--accent);display:inline-block;}
  header p{margin:2px 0 0; font-size:12.5px; color:var(--muted);}
  .stats{display:flex; gap:10px; flex-wrap:wrap;}
  .stat{
    background:var(--panel); border:1px solid var(--border); border-radius:10px;
    padding:8px 14px; min-width:92px; text-align:center;
  }
  .stat .n{font-size:20px; font-weight:700;}
  .stat .l{font-size:10.5px; color:var(--muted); text-transform:uppercase; letter-spacing:.04em;}
  .layout{display:flex; min-height:calc(100vh - 78px);}
  .sidebar{
    width:300px; min-width:240px; border-right:1px solid var(--border);
    padding:14px; overflow-y:auto; background:var(--panel);
  }
  .sidebar button.new{
    width:100%; padding:10px; border-radius:8px; border:none; background:var(--accent);
    color:#fff; font-weight:600; cursor:pointer; margin-bottom:14px; font-size:14px;
  }
  .sidebar button.new:hover{background:var(--accent2);}
  .claim-card{
    background:var(--panel2); border:1px solid var(--border); border-radius:10px;
    padding:10px 12px; margin-bottom:8px; cursor:pointer; transition:border-color .15s;
  }
  .claim-card:hover{border-color:var(--accent);}
  .claim-card.active{border-color:var(--accent); background:#1a2433;}
  .claim-card .top{display:flex; justify-content:space-between; align-items:center; font-size:13px; font-weight:600;}
  .claim-card .sub{font-size:11.5px; color:var(--muted); margin-top:3px;}
  .badges{display:flex; gap:5px; margin-top:6px; flex-wrap:wrap;}
  .badge{
    font-size:9.5px; padding:2px 7px; border-radius:20px; font-weight:700; text-transform:uppercase; letter-spacing:.03em;
  }
  .badge.stage{background:#2a3140; color:var(--text);}
  .badge.late{background:#4a2d2d; color:var(--red);}
  .badge.pattern{background:#3a2d4a; color:var(--purple);}
  .badge.dispute{background:#4a3d1f; color:var(--yellow);}
  .badge.decided{background:#1f4a39; color:var(--green);}
  .empty-side{color:var(--muted); font-size:13px; text-align:center; margin-top:30px;}
  main{flex:1; padding:22px; overflow-y:auto;}
  .welcome{max-width:560px; margin:60px auto; text-align:center; color:var(--muted);}
  .welcome h2{color:var(--text);}
  .card{
    background:var(--panel); border:1px solid var(--border); border-radius:14px;
    padding:20px; margin-bottom:18px;
  }
  .card h3{margin:0 0 4px; font-size:16px;}
  .card .desc{font-size:12.5px; color:var(--muted); margin-bottom:14px;}
  .progress{display:flex; margin-bottom:22px; border-radius:10px; overflow:hidden; border:1px solid var(--border);}
  .progress .seg{flex:1; padding:10px 8px; text-align:center; font-size:12px; font-weight:600; background:var(--panel); color:var(--muted); position:relative;}
  .progress .seg.done{background:#1f4a39; color:var(--green);}
  .progress .seg.current{background:var(--accent); color:#fff;}
  .progress .seg:not(:last-child)::after{content:"›"; position:absolute; right:-2px; top:8px; color:var(--muted);}
  label{display:block; font-size:12px; color:var(--muted); margin:10px 0 4px; font-weight:600;}
  input[type=text], input[type=number], input[type=date], textarea, select{
    width:100%; padding:9px 10px; border-radius:8px; border:1px solid var(--border);
    background:var(--panel2); color:var(--text); font-size:13.5px; font-family:inherit;
  }
  textarea{min-height:70px; resize:vertical;}
  .row{display:flex; gap:12px; flex-wrap:wrap;}
  .row > div{flex:1; min-width:180px;}
  .checks label{display:flex; align-items:center; gap:8px; font-size:13px; color:var(--text); font-weight:400; cursor:pointer;}
  .checks input{width:auto;}
  .flag{
    margin-top:10px; padding:10px 12px; border-radius:8px; font-size:12.5px;
    border-left:3px solid var(--yellow); background:#241f14; color:#e8d9a8;
  }
  .flag.red{border-color:var(--red); background:#241414; color:#f0c4c4;}
  .flag.green{border-color:var(--green); background:#132419; color:#bfe9d4;}
  .flag.purple{border-color:var(--purple); background:#1f1424; color:#d9c6f0;}
  .actions{display:flex; gap:10px; margin-top:16px; flex-wrap:wrap;}
  button{
    padding:9px 16px; border-radius:8px; border:1px solid var(--border); background:var(--panel2);
    color:var(--text); cursor:pointer; font-size:13.5px; font-weight:600;
  }
  button.primary{background:var(--accent); border-color:var(--accent); color:#fff;}
  button.primary:hover{background:var(--accent2);}
  button.danger{background:#3a1f1f; border-color:#5a2c2c; color:#f0b8b8;}
  button.ghost{background:transparent;}
  button:disabled{opacity:.4; cursor:not-allowed;}
  .msg-box{
    background:var(--panel2); border:1px dashed var(--border); border-radius:10px;
    padding:14px; font-size:13px; line-height:1.6; white-space:pre-wrap; margin-top:10px;
  }
  .modal-bg{
    position:fixed; inset:0; background:rgba(0,0,0,.6); display:flex; align-items:center; justify-content:center;
    padding:16px; z-index:50;
  }
  .modal{
    background:var(--panel); border:1px solid var(--border); border-radius:14px; padding:22px;
    max-width:520px; width:100%; max-height:90vh; overflow-y:auto;
  }
  .modal h3{margin-top:0;}
  .hidden{display:none !important;}
  footer{text-align:center; padding:14px; color:var(--muted); font-size:11.5px; border-top:1px solid var(--border);}
  code{background:var(--panel2); padding:1px 6px; border-radius:4px; font-size:12px;}
  @media (max-width:760px){
    .layout{flex-direction:column;}
    .sidebar{width:100%; border-right:none; border-bottom:1px solid var(--border); max-height:260px;}
  }
</style>
</head>
<body>

<header>
  <div>
    <h1><span class="dot"></span> ClaimFlow</h1>
    <p>Post-rental damage claim resolution — intake through follow-up</p>
  </div>
  <div class="stats" id="stats"></div>
</header>

<div class="layout">
  <div class="sidebar">
    <button class="new" onclick="openNewClaimModal()">+ New Claim</button>
    <div id="claimList"></div>
  </div>
  <main id="main"></main>
</div>

<footer>ClaimFlow prototype — data is stored only in this browser (localStorage), nothing leaves your device.</footer>

<div id="modalRoot"></div>

<script>
/* ---------- Data layer ---------- */
const STORAGE_KEY = "claimflow_claims_v1";
let claims = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
let activeId = null;

function save(){ localStorage.setItem(STORAGE_KEY, JSON.stringify(claims)); render(); }

function uid(){ return Math.random().toString(36).slice(2,9); }

function newTicketNumber(){
  const n = 5000 + claims.length + 1;
  return n;
}

function hoursBetween(a,b){ return Math.abs(new Date(b) - new Date(a)) / 36e5; }

function daysBetween(a,b){ return Math.abs(new Date(b) - new Date(a)) / 864e5; }

/* ---------- Claim factory ---------- */
function createClaim(data){
  const claim = {
    id: uid(),
    ticket: newTicketNumber(),
    createdAt: new Date().toISOString(),
    stage: "intake", // intake -> document -> resolve -> followup -> closed
    operator: data.operator,
    renter: data.renter,
    vehicle: data.vehicle,
    bookingId: data.bookingId,
    returnDate: data.returnDate,
    claimFiledDate: data.claimFiledDate,
    description: data.description,
    damageLocation: data.damageLocation,
    damageSize: data.damageSize,
    photoAngles: data.photoAngles, // bool: 3 angles provided
    structuredIntakeNeeded: data.structuredIntakeNeeded,
    // document stage
    hasCheckoutPhotos: null,
    hasCheckinPhotos: null,
    fallbackEvidence: "",
    repairEstimate: null,
    renterResponded: null,
    renterAccount: "",
    slaDeadline: null,
    // resolve stage
    evidenceVerdict: "", // clear | pre-existing | inconclusive
    decision: "", // approve | decline | escalate-insurance
    chargeAmount: null,
    disputeRaised: false,
    disputeNote: "",
    // follow-up
    paymentMethod: "", // renter-charge | insurance | covered-by-1now
    outcomeDate: "",
    trustSafetyFlag: false,
    closedAt: null
  };
  claims.push(claim);
  save();
  return claim;
}

function getClaim(id){ return claims.find(c => c.id === id); }

function operatorClaimCount(operator, excludeId){
  return claims.filter(c => c.operator.trim().toLowerCase() === operator.trim().toLowerCase() && c.id !== excludeId).length + 1;
}

/* ---------- Derived flags ---------- */
function isLate(claim){
  return hoursBetween(claim.returnDate, claim.claimFiledDate) > 48;
}
function needsFallbackEvidence(claim){
  return claim.hasCheckoutPhotos === false;
}
function escalateInsurance(claim){
  return claim.repairEstimate !== null && Number(claim.repairEstimate) > 750;
}
function patternFlag(claim){
  return operatorClaimCount(claim.operator, claim.id) >= 3;
}

/* ---------- Rendering ---------- */
function render(){
  renderStats();
  renderList();
  renderMain();
}

function renderStats(){
  const total = claims.length;
  const late = claims.filter(isLate).length;
  const disputed = claims.filter(c => c.disputeRaised && c.stage !== "closed").length;
  const flagged = claims.filter(c => patternFlag(c) || c.trustSafetyFlag).length;
  document.getElementById("stats").innerHTML = `
    <div class="stat"><div class="n">${total}</div><div class="l">Claims</div></div>
    <div class="stat"><div class="n">${late}</div><div class="l">Late</div></div>
    <div class="stat"><div class="n">${disputed}</div><div class="l">Disputed</div></div>
    <div class="stat"><div class="n">${flagged}</div><div class="l">T&S Review</div></div>
  `;
}

function stageLabel(s){
  return {intake:"Intake", document:"Document", resolve:"Resolve", followup:"Follow-up", closed:"Closed"}[s] || s;
}

function renderList(){
  const list = document.getElementById("claimList");
  if(claims.length === 0){
    list.innerHTML = `<div class="empty-side">No claims yet.<br>Click "New Claim" to start the flow.</div>`;
    return;
  }
  list.innerHTML = claims.slice().reverse().map(c => {
    const badges = [];
    badges.push(`<span class="badge stage">${stageLabel(c.stage)}</span>`);
    if(isLate(c)) badges.push(`<span class="badge late">Late claim</span>`);
    if(patternFlag(c) || c.trustSafetyFlag) badges.push(`<span class="badge pattern">T&S review</span>`);
    if(c.disputeRaised) badges.push(`<span class="badge dispute">Disputed</span>`);
    if(c.stage === "closed") badges.push(`<span class="badge decided">Closed</span>`);
    return `
      <div class="claim-card ${c.id===activeId?'active':''}" onclick="selectClaim('${c.id}')">
        <div class="top"><span>#${c.ticket}</span><span>${c.vehicle || ""}</span></div>
        <div class="sub">${c.operator} vs ${c.renter}</div>
        <div class="badges">${badges.join("")}</div>
      </div>
    `;
  }).join("");
}

function selectClaim(id){ activeId = id; renderMain(); renderList(); }

function renderMain(){
  const main = document.getElementById("main");
  const claim = getClaim(activeId);
  if(!claim){
    main.innerHTML = `
      <div class="welcome">
        <h2>Damage claim resolution flow</h2>
        <p>This prototype walks a support agent through the full loop for a post-rental damage claim: <b>Intake → Document → Resolve → Follow-up</b> — including the messy edge cases (late claims, missing checkout photos, disputes, inflated estimates, repeat-filer patterns).</p>
        <p>Select a claim on the left, or create a new one to begin.</p>
      </div>`;
    return;
  }
  main.innerHTML = renderClaimDetail(claim);
}

function stageIndex(stage){
  return ["intake","document","resolve","followup","closed"].indexOf(stage);
}

function progressBar(claim){
  const stages = ["intake","document","resolve","followup"];
  const idx = stageIndex(claim.stage);
  return `<div class="progress">` + stages.map((s,i) => {
    let cls = "";
    if(i < idx || claim.stage === "closed") cls = "done";
    else if(i === idx) cls = "current";
    return `<div class="seg ${cls}">${stageLabel(s)}</div>`;
  }).join("") + `</div>`;
}

function renderClaimDetail(c){
  let html = `<div class="card">
    <h3>Ticket #${c.ticket} — ${c.vehicle || "Vehicle"}</h3>
    <div class="desc">Operator: <b>${c.operator}</b> &nbsp;|&nbsp; Renter: <b>${c.renter}</b> &nbsp;|&nbsp; Booking: ${c.bookingId || "—"}</div>
  </div>`;
  html += progressBar(c);

  if(c.stage === "intake") html += renderIntakeStage(c);
  else if(c.stage === "document") html += renderDocumentStage(c);
  else if(c.stage === "resolve") html += renderResolveStage(c);
  else if(c.stage === "followup") html += renderFollowupStage(c);
  else if(c.stage === "closed") html += renderClosedStage(c);

  html += `<div class="actions"><button class="danger ghost" onclick="deleteClaim('${c.id}')">Delete this claim</button></div>`;
  return html;
}

/* ---------- Stage 1: Intake ---------- */
function renderIntakeStage(c){
  const late = isLate(c);
  let flags = "";
  if(late){
    flags += `<div class="flag">Filed ${daysBetween(c.returnDate,c.claimFiledDate).toFixed(1)} day(s) after return — outside the 48-hour window. This will be logged as a <b>Late Claim</b> and held to a higher evidence bar.</div>`;
  }
  if(c.structuredIntakeNeeded){
    flags += `<div class="flag">Description was vague — a structured intake form (location, size, 3 photo angles) should be sent to the operator before proceeding.</div>`;
  }
  return `
    <div class="card">
      <h3>1. Intake</h3>
      <div class="desc">Confirm the rental ended, check the claim window, and pull checkout/check-in photo sets.</div>
      <p><b>Description:</b> ${c.description}</p>
      <p><b>Damage location:</b> ${c.damageLocation || "—"} &nbsp;|&nbsp; <b>Size:</b> ${c.damageSize || "—"}</p>
      <p><b>Return date:</b> ${fmt(c.returnDate)} &nbsp;|&nbsp; <b>Claim filed:</b> ${fmt(c.claimFiledDate)}</p>
      ${flags}
      <div class="actions">
        <button class="primary" onclick="advanceStage('${c.id}','document')">Ticket open, window checked → Start evidence packet</button>
      </div>
    </div>
  `;
}

/* ---------- Stage 2: Document ---------- */
function renderDocumentStage(c){
  const form = c._editingDoc;
  return `
    <div class="card">
      <h3>2. Document</h3>
      <div class="desc">Line up checkout vs. check-in photos, request a repair estimate, and get the renter's account.</div>

      <label>Do checkout (pre-rental) photos exist for this booking?</label>
      <select onchange="updateClaim('${c.id}', {hasCheckoutPhotos: this.value === 'yes'})">
        <option value="">Select…</option>
        <option value="yes" ${c.hasCheckoutPhotos===true?"selected":""}>Yes</option>
        <option value="no" ${c.hasCheckoutPhotos===false?"selected":""}>No</option>
      </select>
      ${c.hasCheckoutPhotos===false ? `
        <div class="flag red">No checkout photos on file. Falling back to telemetry/impact log or the prior rental's check-in photos as baseline.</div>
        <label>Fallback evidence used</label>
        <textarea onchange="updateClaim('${c.id}', {fallbackEvidence: this.value})" placeholder="e.g. Impact log shows a jolt at 14:02 on return day; prior rental's check-in photos show no damage in this area.">${c.fallbackEvidence || ""}</textarea>
      ` : ""}

      <label>Repair estimate ($)</label>
      <input type="number" min="0" value="${c.repairEstimate ?? ""}" onchange="updateClaim('${c.id}', {repairEstimate: this.value === '' ? null : Number(this.value)})" placeholder="e.g. 180">
      ${escalateInsurance(c) ? `<div class="flag purple">Estimate exceeds the $750 threshold — this will route to insurance instead of a direct card charge at the Resolve stage.</div>` : ""}

      <label>Did the renter respond with their account of the trip?</label>
      <select onchange="updateClaim('${c.id}', {renterResponded: this.value === 'yes'})">
        <option value="">Select…</option>
        <option value="yes" ${c.renterResponded===true?"selected":""}>Yes</option>
        <option value="no" ${c.renterResponded===false?"selected":""}>No — proceeding on 48h SLA</option>
      </select>
      ${c.renterResponded===true ? `
        <label>Renter's account</label>
        <textarea onchange="updateClaim('${c.id}', {renterAccount: this.value})">${c.renterAccount || ""}</textarea>
      ` : ""}
      ${c.renterResponded===false ? `<div class="flag">No response — proceeding with available evidence; renter was given the chance to reply (48h SLA noted on file).</div>` : ""}

      <div class="actions">
        <button class="ghost" onclick="advanceStage('${c.id}','intake')">← Back</button>
        <button class="primary" ${canAdvanceToResolve(c) ? "" : "disabled"} onclick="advanceStage('${c.id}','resolve')">Evidence packet complete → Resolve liability</button>
      </div>
    </div>
  `;
}
function canAdvanceToResolve(c){
  return c.hasCheckoutPhotos !== null && c.repairEstimate !== null && c.renterResponded !== null;
}

/* ---------- Stage 3: Resolve ---------- */
function renderResolveStage(c){
  const late = isLate(c);
  return `
    <div class="card">
      <h3>3. Resolve</h3>
      <div class="desc">Decide liability from the dated evidence — not either party's word.</div>

      <label>Evidence verdict</label>
      <select onchange="updateClaim('${c.id}', {evidenceVerdict: this.value})">
        <option value="">Select…</option>
        <option value="clear" ${c.evidenceVerdict==="clear"?"selected":""}>Clear — damage visible in check-in, absent at checkout</option>
        <option value="pre-existing" ${c.evidenceVerdict==="pre-existing"?"selected":""}>Pre-existing / not caused this rental</option>
        <option value="inconclusive" ${c.evidenceVerdict==="inconclusive"?"selected":""}>Inconclusive</option>
      </select>

      ${c.evidenceVerdict==="inconclusive" ? `<div class="flag">Inconclusive evidence always decides for the renter — the operator controls whether good checkout evidence exists, the renter doesn't.</div>` : ""}
      ${late ? `<div class="flag red">This is a Late Claim — only approve if evidence clearly isolates the damage to this specific rental window.</div>` : ""}
      ${escalateInsurance(c) ? `<div class="flag purple">Estimate ($${c.repairEstimate}) is above $750 → route to insurance, not a direct card charge.</div>` : ""}

      <label class="checks"><input type="checkbox" ${c.disputeRaised?"checked":""} onchange="updateClaim('${c.id}', {disputeRaised: this.checked})"> Renter disputed the charge before it was finalized</label>
      ${c.disputeRaised ? `
        <label>Dispute note / new evidence provided</label>
        <textarea onchange="updateClaim('${c.id}', {disputeNote: this.value})" placeholder="Charge is paused, not reversed. Re-review within a fixed 48-hour SLA.">${c.disputeNote || ""}</textarea>
        <div class="flag">Charge paused. Re-review with any new evidence against a fixed 48-hour SLA before finalizing.</div>
      ` : ""}

      <label>Decision</label>
      <select onchange="updateClaim('${c.id}', {decision: this.value})">
        <option value="">Select…</option>
        <option value="approve" ${c.decision==="approve"?"selected":""}>Approve charge against renter</option>
        <option value="decline" ${c.decision==="decline"?"selected":""}>Decline claim</option>
        <option value="escalate-insurance" ${c.decision==="escalate-insurance"?"selected":""}>Escalate to insurance</option>
      </select>

      ${c.decision==="approve" ? `
        <label>Charge amount ($)</label>
        <input type="number" value="${c.chargeAmount ?? c.repairEstimate ?? ""}" onchange="updateClaim('${c.id}', {chargeAmount: this.value===''?null:Number(this.value)})">
      ` : ""}

      <div class="actions">
        <button class="ghost" onclick="advanceStage('${c.id}','document')">← Back</button>
        <button class="primary" ${c.decision ? "" : "disabled"} onclick="advanceStage('${c.id}','followup')">Liability decided → Notify &amp; follow up</button>
      </div>
    </div>
  `;
}

/* ---------- Stage 4: Follow-up ---------- */
function renderFollowupStage(c){
  const pattern = patternFlag(c);
  return `
    <div class="card">
      <h3>4. Follow-up</h3>
      <div class="desc">Close the loop with both sides and log the outcome.</div>

      ${pattern ? `<div class="flag purple">${c.operator} has filed ${operatorClaimCount(c.operator, c.id)} claims — quietly routed to Trust &amp; Safety for a pattern review (not auto-approved).</div>` : ""}

      <label class="checks"><input type="checkbox" ${c.trustSafetyFlag?"checked":""} onchange="updateClaim('${c.id}', {trustSafetyFlag: this.checked})"> Manually flag for Trust &amp; Safety review</label>

      <label>How is the operator being made whole?</label>
      <select onchange="updateClaim('${c.id}', {paymentMethod: this.value})">
        <option value="">Select…</option>
        <option value="renter-charge" ${c.paymentMethod==="renter-charge"?"selected":""}>Renter charge / deposit</option>
        <option value="insurance" ${c.paymentMethod==="insurance"?"selected":""}>Insurance payout</option>
        <option value="covered-by-1now" ${c.paymentMethod==="covered-by-1now"?"selected":""}>Covered directly (1Now process gap)</option>
        <option value="none" ${c.paymentMethod==="none"?"selected":""}>No payout — claim declined</option>
      </select>

      <label>Outcome date</label>
      <input type="date" value="${c.outcomeDate || ""}" onchange="updateClaim('${c.id}', {outcomeDate: this.value})">

      <div class="msg-box">${buildOperatorMessage(c)}</div>
      <div class="actions">
        <button onclick="copyMessage('${c.id}')">Copy message to operator</button>
      </div>

      <div class="actions">
        <button class="ghost" onclick="advanceStage('${c.id}','resolve')">← Back</button>
        <button class="primary" ${c.paymentMethod && c.outcomeDate ? "" : "disabled"} onclick="closeClaim('${c.id}')">Operator paid, renter notified → Close ticket</button>
      </div>
    </div>
  `;
}

/* ---------- Stage 5: Closed ---------- */
function renderClosedStage(c){
  return `
    <div class="card">
      <h3>Closed</h3>
      <div class="desc">This claim has been resolved and the loop is closed.</div>
      <div class="flag green">Closed on ${fmt(c.outcomeDate)}. Decision: <b>${c.decision}</b>. Payment method: <b>${c.paymentMethod}</b>.</div>
      <div class="msg-box">${buildOperatorMessage(c)}</div>
      <div class="actions">
        <button onclick="copyMessage('${c.id}')">Copy evidence summary / operator message</button>
        <button class="ghost" onclick="advanceStage('${c.id}','followup')">Reopen follow-up</button>
      </div>
    </div>
  `;
}

/* ---------- Message builder ---------- */
function buildOperatorMessage(c){
  if(c.decision === "decline"){
    return `Hi ${c.operator} — we reviewed the ${c.damageLocation || "reported damage"} on ticket #${c.ticket}. Based on the available evidence, we were not able to approve this claim (${c.evidenceVerdict === "inconclusive" ? "evidence was inconclusive" : "damage appears pre-existing or unrelated to this rental"}). ${c.fallbackEvidence ? "Note: no checkout photos were on file for this booking — capturing them consistently will strengthen any future claim." : ""}`;
  }
  const amount = c.chargeAmount ?? c.repairEstimate ?? "—";
  const via = c.paymentMethod === "insurance" ? "an insurance payout" : c.paymentMethod === "covered-by-1now" ? "a direct 1Now coverage" : `${c.renter}'s deposit`;
  return `Hi ${c.operator} — we reviewed the ${c.damageLocation || "reported damage"} on ticket #${c.ticket}. Your checkout photos ${c.hasCheckoutPhotos ? `from ${fmt(c.returnDate)} show it clean` : "were not available for this booking, so we used fallback evidence"}; the renter's check-in photos show the mark. We've arranged $${amount} via ${via}, and ${c.renter} has been notified with a chance to respond if they disagree. You don't need to do anything further — we'll update you if that changes.`;
}

function copyMessage(id){
  const c = getClaim(id);
  const text = buildOperatorMessage(c);
  navigator.clipboard?.writeText(text).then(()=>{
    alert("Message copied to clipboard.");
  }).catch(()=>{
    prompt("Copy this message:", text);
  });
}

/* ---------- Mutations ---------- */
function updateClaim(id, patch){
  const c = getClaim(id);
  Object.assign(c, patch);
  save();
}
function advanceStage(id, stage){
  updateClaim(id, {stage});
}
function closeClaim(id){
  updateClaim(id, {stage:"closed", closedAt:new Date().toISOString()});
}
function deleteClaim(id){
  if(!confirm("Delete this claim? This cannot be undone.")) return;
  claims = claims.filter(c => c.id !== id);
  if(activeId === id) activeId = null;
  save();
}

/* ---------- New claim modal ---------- */
function openNewClaimModal(){
  const today = new Date().toISOString().slice(0,10);
  document.getElementById("modalRoot").innerHTML = `
    <div class="modal-bg" onclick="if(event.target===this) closeModal()">
      <div class="modal">
        <h3>New damage claim</h3>
        <label>Operator name</label>
        <input type="text" id="f_operator" placeholder="e.g. Downtown Fleet LLC">
        <label>Renter name</label>
        <input type="text" id="f_renter" placeholder="e.g. J. Alvarez">
        <div class="row">
          <div><label>Vehicle</label><input type="text" id="f_vehicle" placeholder="2022 Honda Civic"></div>
          <div><label>Booking ID</label><input type="text" id="f_booking" placeholder="BK-10234"></div>
        </div>
        <div class="row">
          <div><label>Rental return date</label><input type="date" id="f_return" value="${today}"></div>
          <div><label>Claim filed date</label><input type="date" id="f_filed" value="${today}"></div>
        </div>
        <label>Damage description</label>
        <textarea id="f_desc" placeholder="e.g. Scuff and dent on rear bumper, passenger side"></textarea>
        <div class="row">
          <div><label>Damage location</label><input type="text" id="f_loc" placeholder="Rear bumper"></div>
          <div><label>Approx. size</label><input type="text" id="f_size" placeholder="3 inches"></div>
        </div>
        <label class="checks"><input type="checkbox" id="f_photos3"> Operator provided photos from 3 angles</label>
        <div class="actions">
          <button class="ghost" onclick="closeModal()">Cancel</button>
          <button class="primary" onclick="submitNewClaim()">Create claim</button>
        </div>
      </div>
    </div>
  `;
}
function closeModal(){ document.getElementById("modalRoot").innerHTML = ""; }
function submitNewClaim(){
  const operator = document.getElementById("f_operator").value.trim();
  const renter = document.getElementById("f_renter").value.trim();
  if(!operator || !renter){ alert("Operator and renter name are required."); return; }
  const photos3 = document.getElementById("f_photos3").checked;
  const desc = document.getElementById("f_desc").value.trim();
  const claim = createClaim({
    operator, renter,
    vehicle: document.getElementById("f_vehicle").value.trim(),
    bookingId: document.getElementById("f_booking").value.trim(),
    returnDate: document.getElementById("f_return").value,
    claimFiledDate: document.getElementById("f_filed").value,
    description: desc || "(no description provided)",
    damageLocation: document.getElementById("f_loc").value.trim(),
    damageSize: document.getElementById("f_size").value.trim(),
    photoAngles: photos3,
    structuredIntakeNeeded: !photos3 || desc.length < 15
  });
  closeModal();
  selectClaim(claim.id);
}

function fmt(d){
  if(!d) return "—";
  try{ return new Date(d).toLocaleDateString(undefined,{year:"numeric",month:"short",day:"numeric"}); }
  catch(e){ return d; }
}

/* ---------- Seed demo data on first run ---------- */
function seedIfEmpty(){
  if(claims.length > 0) return;
  const c1 = createClaim({
    operator:"Downtown Fleet LLC", renter:"J. Alvarez", vehicle:"2022 Honda Civic", bookingId:"BK-10234",
    returnDate:"2026-09-28", claimFiledDate:"2026-09-29",
    description:"Scuff and dent on rear bumper, passenger side", damageLocation:"Rear bumper", damageSize:"3 inches",
    photoAngles:true, structuredIntakeNeeded:false
  });
  updateClaim(c1.id, {hasCheckoutPhotos:true, repairEstimate:180, renterResponded:false});
}
seedIfEmpty();
render();
</script>
</body>
</html>
