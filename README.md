<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>BedMatrix | Hospital Bed & Blood Bank Tracker</title>

  <!-- Google Fonts -->
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">

  <!-- Leaflet CSS -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />

  <style>
    :root {
      --primary-color: #3B82F6;
      --secondary-color: #10B981;
      --bg-light: #F9FAFB;
      --text-dark: #1F2937;
      --text-gray: #4B5563;
      --white: #FFFFFF;
    }

    * { margin:0; padding:0; box-sizing:border-box; font-family:'Inter', sans-serif; }
    html { scroll-behavior: smooth; }
    body { background: var(--bg-light); color:var(--text-dark); }

    nav {
      background: var(--white);
      padding:1rem 2rem;
      display:flex;
      justify-content:space-between;
      align-items:center;
      box-shadow:0 2px 6px rgba(0,0,0,0.08);
      position: sticky;
      top:0;
      z-index:1000;
    }
    nav h1 { color: var(--primary-color); font-weight:700; font-size:1.6rem; }
    nav ul { list-style:none; display:flex; gap:1.2rem; align-items:center; }
    nav ul li a { text-decoration:none; color: var(--text-dark); font-weight:500; cursor:pointer;}
    nav ul li a:hover { color: var(--primary-color); }

    section { padding: 3rem 2rem; }
    h2.section-title { text-align:center; font-size:2.2rem; font-weight:700; margin-bottom:1.5rem; color:var(--text-dark); }

    /* Hero */
    .hero { text-align:center; padding:4rem 2rem; background: linear-gradient(to right,#E0F2FE,#F0FDFA);}
    .hero h2 { font-size:2.4rem; font-weight:700; color:var(--text-dark); }
    .hero p { max-width:780px; margin:0.8rem auto; color:var(--text-gray); font-size:1.05rem; }

    .hero button { background: var(--primary-color); color:white; padding:0.8rem 2rem; border:none; border-radius:8px; cursor:pointer; font-weight:600; }

    /* Features */
    .features-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(220px,1fr)); gap:1.5rem; max-width:1200px; margin:0 auto;}
    .feature-card { background: var(--white); padding:1.5rem; border-radius:12px; box-shadow:0 6px 20px rgba(0,0,0,0.05); text-align:center; transition: transform 0.28s ease, box-shadow 0.28s ease;}
    .feature-card:hover { transform:translateY(-6px); box-shadow:0 12px 30px rgba(0,0,0,0.08);}
    .feature-icon { width:56px; height:56px; margin-bottom:0.75rem; object-fit:contain; }
    .feature-title { font-size:1.1rem; font-weight:600; margin-bottom:0.45rem; color: var(--primary-color);}
    .feature-desc { font-size:0.95rem; color:var(--text-gray); }

    /* Dashboard Table */
    .dashboard table { width:100%; max-width:1100px; margin:0 auto; border-collapse:collapse; box-shadow:0 4px 20px rgba(0,0,0,0.05);}
    th,td { padding:0.9rem; text-align:center; border-bottom:1px solid #E5E7EB;}
    th { background: var(--primary-color); color:white; font-weight:600;}
    tr:hover { background:#F3F4F6; transition:0.2s;}
    .status { padding:0.35rem 0.7rem; border-radius:6px; font-weight:600; display:inline-block;}
    .available { background:#DCFCE7; color:#166534; }
    .fewleft { background:#FEF9C3; color:#92400E; }
    .full { background:#FEE2E2; color:#991B1B; }

    /* FAQ */
    .faq-item { max-width:800px; margin:1rem auto; padding:1rem; background: var(--white); border-radius:12px; box-shadow:0 6px 20px rgba(0,0,0,0.05);}
    .faq-question { font-weight:600; cursor:pointer; }
    .faq-answer { margin-top:0.5rem; display:none; color:var(--text-gray); }

    /* Login */
    .login-form { max-width:400px; margin:2rem auto; padding:2rem; background: var(--white); border-radius:12px; box-shadow:0 6px 20px rgba(0,0,0,0.05);}
    .login-form input, .login-form button { width:100%; padding:0.8rem; margin:0.5rem 0; border-radius:8px; border:1px solid #D1D5DB; }
    .login-form button { background: var(--primary-color); color:white; border:none; cursor:pointer; }

    /* Map */
    #map { width:100%; height:64vh; border-radius:12px; box-shadow:0 4px 12px rgba(0,0,0,0.08); margin:1.5rem 0; }

    /* Blood Banks Table */
    #blood-bank-section table { border-radius:8px; overflow:hidden; background:var(--white); }

    /* Floating Chatbot */
    .chatbot-toggle { position:fixed; bottom:20px; right:20px; background:var(--secondary-color); color:white; border:none; border-radius:50%; width:60px; height:60px; font-size:1.6rem; cursor:pointer; z-index:1001; box-shadow:0 4px 15px rgba(0,0,0,0.2);}
    .chatbot-container { position:fixed; bottom:90px; right:20px; width:360px; height:480px; background: var(--white); border-radius:16px; box-shadow:0 6px 25px rgba(0,0,0,0.3); display:none; flex-direction:column; overflow:hidden; z-index:1002;}
    .chat-header { background: var(--primary-color); color:white; text-align:center; padding:1rem; font-weight:600; }
    .chat-body { flex:1; padding:0.8rem; overflow-y:auto; display:flex; flex-direction:column; gap:0.5rem;}
    .chat-msg { padding:0.6rem 1rem; border-radius:12px; max-width:80%; word-wrap:break-word;}
    .bot-msg { background:#E0F2FE; align-self:flex-start; }
    .user-msg { background:#DCFCE7; align-self:flex-end; }
    .chat-input { display:flex; border-top:1px solid #E5E7EB; }
    .chat-input input { flex:1; border:none; padding:0.8rem; outline:none; }
    .chat-input button { border:none; background: var(--primary-color); color:white; padding:0 1rem; cursor:pointer; }

    .legend { background:white; padding:8px; border-radius:8px; box-shadow:0 4px 12px rgba(0,0,0,0.08); font-size:0.9rem; }
    .legend-row { display:flex; gap:8px; align-items:center; margin-top:6px; }

    footer { background: var(--white); text-align:center; padding:1.5rem; margin-top:2rem; box-shadow:0 -2px 6px rgba(0,0,0,0.06);}
    footer p { color:#4B5563; font-size:0.95rem; }
    @media (max-width:720px) {
      nav { padding:0.8rem 1rem; }
      .hero { padding:3rem 1rem; }
      .chatbot-container { right:12px; width:320px; height:420px; }
    }
  </style>
</head>

<body>

  <!-- Navbar -->
  <nav>
    <h1>🛏️ BedMatrix</h1>
    <ul>
      <li><a href="#home">Home</a></li>
      <li><a href="#features">Features</a></li>
      <li><a href="#dashboard">Dashboard</a></li>
      <li><a href="#faq">FAQ</a></li>
      <li><a href="#login">Login</a></li>
      <li><a href="#map-section">Map</a></li>
      <li><a href="#blood-bank-section">Blood Banks</a></li>
    </ul>
  </nav>

  <!-- Hero -->
  <section class="hero" id="home">
    <h2>Real-Time Hospital Bed Availability</h2>
    <p>Find available beds in General, ICU, Emergency, and Operation wards instantly. Your healthcare decisions simplified.</p>
  </section>

  <!-- Features -->
  <section class="features-section" id="features">
    <h2 class="section-title">Key Features</h2>
    <div class="features-grid">
      <div class="feature-card">
        <img src="https://cdn-icons-png.flaticon.com/512/2966/2966327.png" class="feature-icon" alt="real-time" />
        <h3 class="feature-title">Real-Time Availability</h3>
        <p class="feature-desc">Instant bed updates across all wards.</p>
      </div>
      <div class="feature-card">
        <img src="https://cdn-icons-png.flaticon.com/512/483/483356.png" class="feature-icon" alt="search" />
        <h3 class="feature-title">Search & Filter</h3>
        <p class="feature-desc">Find hospitals by name, ward, or availability.</p>
      </div>
      <div class="feature-card">
        <img src="https://cdn-icons-png.flaticon.com/512/942/942799.png" class="feature-icon" alt="admin" />
        <h3 class="feature-title">Admin Dashboard</h3>
        <p class="feature-desc">Securely update bed records.</p>
      </div>
      <div class="feature-card">
        <img src="https://cdn-icons-png.flaticon.com/512/190/190411.png" class="feature-icon" alt="alerts" />
        <h3 class="feature-title">Smart Alerts</h3>
        <p class="feature-desc">Color-coded bed availability status.</p>
      </div>

      <!-- NEW: Blood Bank Locator feature card -->
      <div class="feature-card">
        <img src="https://cdn-icons-png.flaticon.com/512/1048/1048953.png" class="feature-icon" alt="blood-bank" />
        <h3 class="feature-title">Blood Bank Locator</h3>
        <p class="feature-desc">Find nearest blood banks and check available blood groups instantly.</p>
      </div>
    </div>
  </section>

  <!-- Dashboard -->
  <section class="dashboard" id="dashboard">
    <h2 class="section-title">Live Bed Dashboard</h2>
    <table>
      <thead>
        <tr>
          <th>Hospital</th><th>General</th><th>Emergency</th><th>ICU</th><th>Operation</th><th>Status</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>AIIMS</td><td>25/50</td><td>8/15</td><td>2/10</td><td>1/5</td><td><span class="status fewleft">Few Left</span></td>
        </tr>
        <tr>
          <td>Fortis</td><td>10/40</td><td>2/10</td><td>1/5</td><td>0/5</td><td><span class="status available">Available</span></td>
        </tr>
      </tbody>
    </table>
  </section>

  <!-- FAQ -->
  <section class="faq-section" id="faq">
    <h2 class="section-title">FAQ</h2>

    <div class="faq-item">
      <div class="faq-question">How do I check bed availability?</div>
      <div class="faq-answer">Use the live map or the dashboard table to see the current availability of beds in different wards.</div>
    </div>

    <div class="faq-item">
      <div class="faq-question">How can I contact hospitals?</div>
      <div class="faq-answer">Click on a hospital marker on the map to see contact information and location.</div>
    </div>

    <div class="faq-item">
      <div class="faq-question">Is my data private?</div>
      <div class="faq-answer">Yes, absolutely. While location data is public, your personal information (name, contact details) is kept confidential.</div>
    </div>
  </section>

  <!-- Login Section -->
  <section id="login">
    <h2 class="section-title">Login</h2>

    <div class="login-tabs" style="display:flex; justify-content:center; gap:2rem; margin-bottom:1.5rem;">
      <button onclick="showLogin('user')" id="user-btn" style="padding:0.8rem 2rem; border:none; border-radius:8px; background:var(--primary-color); color:white; cursor:pointer;">User Login</button>
      <button onclick="showLogin('official')" id="official-btn" style="padding:0.8rem 2rem; border:none; border-radius:8px; background:#E5E7EB; color:var(--text-dark); cursor:pointer;">Official Login</button>
    </div>

    <!-- User Login Form -->
    <form class="login-form" id="user-login" style="display:block;">
      <p style="margin-bottom:1rem;">Welcome!<br>Enter your details to login or sign up</p>
      <input type="text" placeholder="Phone Number or Gmail" required />
      <button type="button">Send OTP</button>
    </form>

    <!-- Official Login Form -->
    <form class="login-form" id="official-login" style="display:none;">
      <p style="margin-bottom:1rem;">Login with your employee credentials</p>
      <input type="text" placeholder="Employee ID" required />
      <input type="password" placeholder="Password" required />
      <a href="#" style="font-size:0.85rem; color:var(--primary-color); text-decoration:none; display:block; margin-bottom:0.5rem;">Forgot Password?</a>
      <button type="submit">Login</button>
    </form>
  </section>

  <!-- Map -->
  <section id="map-section">
    <h2 class="section-title">🏥 Hospitals & 🩸 Blood Banks in Delhi</h2>
    <div id="map"></div>
  </section>

  <!-- Blood Banks Table (NEW) -->
  <section id="blood-bank-section">
    <h2 style="text-align:center; margin-top:0.5rem;">🩸 Nearby Blood Banks</h2>
    <p style="text-align:center; color:#4B5563; max-width:760px; margin:0.5rem auto 1rem;">
      Find your nearest blood banks and check which blood groups are currently available. Click a row to center the map on that blood bank.
    </p>

    <table style="width:90%; max-width:940px; margin:0 auto 3rem; border-collapse:collapse; box-shadow:0 4px 20px rgba(0,0,0,0.05);">
      <thead>
        <tr style="background:var(--primary-color); color:white;">
          <th style="padding:0.9rem;">Blood Bank</th>
          <th style="padding:0.9rem;">Location</th>
          <th style="padding:0.9rem;">Available Blood Groups</th>
        </tr>
      </thead>
      <tbody id="blood-bank-table"></tbody>
    </table>
  </section>

  <!-- Chatbot -->
  <button class="chatbot-toggle" onclick="toggleChatbot()" title="Open chat">💬</button>
  <div class="chatbot-container" id="chatbot">
    <div class="chat-header">🩺 BedMatrix Assistant</div>
    <div class="chat-body" id="chat-body">
      <div class="chat-msg bot-msg">Hello! 👋 I’m your BedMatrix assistant. Ask about nearest hospital, emergency beds, or blood bank availability.</div>
    </div>
    <div class="chat-input">
      <input type="text" id="user-input" placeholder="Try: 'Nearest blood bank' or 'Is O+ available?'" />
      <button onclick="sendMessage()">Send</button>
    </div>
  </div>

  <footer>
    <p>© <span id="year"></span> BedMatrix — demo UI. For production, wire to live APIs and secure admin access.</p>
  </footer>

  <!-- Leaflet JS -->
  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

  <script>
    // small init
    document.getElementById('year').textContent = new Date().getFullYear();

    // FAQ toggle
    document.querySelectorAll('.faq-question').forEach(q=>{
      q.addEventListener('click',()=>{ 
        const ans = q.nextElementSibling;
        ans.style.display = ans.style.display==='block' ? 'none' : 'block';
      });
    });

    // Login toggles
    function showLogin(type){
      const userForm = document.getElementById('user-login');
      const officialForm = document.getElementById('official-login');
      const userBtn = document.getElementById('user-btn');
      const officialBtn = document.getElementById('official-btn');

      if(type==='user'){
        userForm.style.display='block';
        officialForm.style.display='none';
        userBtn.style.background= 'var(--primary-color)';
        userBtn.style.color='white';
        officialBtn.style.background='#E5E7EB';
        officialBtn.style.color='var(--text-dark)';
      } else {
        userForm.style.display='none';
        officialForm.style.display='block';
        officialBtn.style.background= 'var(--primary-color)';
        officialBtn.style.color='white';
        userBtn.style.background='#E5E7EB';
        userBtn.style.color='var(--text-dark)';
      }
    }

    // ===== Map initialization =====
    const map = L.map('map', { zoomControl: true }).setView([28.6139, 77.2090], 12);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{maxZoom:19, attribution:'&copy; OpenStreetMap contributors'}).addTo(map);

    // sample hospitals
    const hospitals = [
      { id: 'H1', name:"AIIMS Hospital", lat:28.5672, lon:77.2100, contact: "+91-11-2658-8500" },
      { id: 'H2', name:"Fortis Hospital", lat:28.5562, lon:77.1000, contact: "+91-11-4123-4567" },
      { id: 'H3', name:"Safdarjung Hospital", lat:28.5675, lon:77.2097, contact: "+91-11-2610-3284" },
      { id: 'H4', name:"Max Healthcare", lat:28.6280, lon:77.2180, contact: "+91-11-4286-1000" }
    ];

    // add hospital markers (default blue markers)
    const hospitalMarkers = {};
    hospitals.forEach(h => {
      const m = L.marker([h.lat, h.lon]).addTo(map).bindPopup(`<b>${h.name}</b><br>Contact: ${h.contact}`);
      hospitalMarkers[h.id] = m;
    });

    // ===== Blood Banks =====
    const bloodBanks = [
      {
        id: 'B1',
        name: "Delhi Red Cross Blood Bank",
        lat: 28.5740,
        lon: 77.2070,
        location: "Central Delhi",
        contact: "+91-11-2334-0001",
        groups: ["A+", "B+", "O+", "AB+", "O-", "A-"]
      },
      {
        id: 'B2',
        name: "AIIMS Blood Bank",
        lat: 28.5678,
        lon: 77.2105,
        location: "Ansari Nagar",
        contact: "+91-11-2658-1234",
        groups: ["A+", "O+", "B-", "AB-"]
      },
      {
        id: 'B3',
        name: "Fortis Blood Bank",
        lat: 28.5565,
        lon: 77.1012,
        location: "Okhla",
        contact: "+91-11-4123-9999",
        groups: ["A+", "O+", "B+", "O-"]
      },
      {
        id: 'B4',
        name: "Apollo Blood Bank",
        lat: 28.5445,
        lon: 77.2693,
        location: "South Delhi",
        contact: "+91-11-2666-7777",
        groups: ["A+", "B+", "O+", "A-", "B-"]
      }
    ];

    // store markers to manipulate later
    const bloodBankMarkers = {};

    bloodBanks.forEach(b => {
      const marker = L.circleMarker([b.lat, b.lon], {
        radius: 9,
        color: "#EF4444",
        weight: 2,
        fillColor: "#FCA5A5",
        fillOpacity: 0.9
      }).addTo(map);

      const popupHtml = `<div style="font-weight:600;">${b.name}</div>
                         <div style="margin-top:6px;"><b>Location:</b> ${b.location}</div>
                         <div style="margin-top:6px;"><b>Contact:</b> ${b.contact}</div>
                         <div style="margin-top:6px;"><b>Available:</b> ${b.groups.join(", ")}</div>`;

      marker.bindPopup(popupHtml);
      bloodBankMarkers[b.id] = marker;
    });

    // Populate blood bank table and add click-to-focus behavior
    const tableBody = document.getElementById('blood-bank-table');
    bloodBanks.forEach(b => {
      const tr = document.createElement('tr');
      tr.style.cursor = 'pointer';
      tr.innerHTML = `
        <td style="padding:0.8rem; border-bottom:1px solid #E5E7EB; text-align:left;">${b.name}</td>
        <td style="padding:0.8rem; border-bottom:1px solid #E5E7EB;">${b.location}</td>
        <td style="padding:0.8rem; border-bottom:1px solid #E5E7EB;">${b.groups.join(", ")}</td>
      `;
      tr.addEventListener('click', () => {
        map.setView([b.lat, b.lon], 14, { animate: true });
        bloodBankMarkers[b.id].openPopup();
      });
      tableBody.appendChild(tr);
    });

    // Add legend control
    const legend = L.control({ position: "bottomright" });
    legend.onAdd = function(map) {
      const div = L.DomUtil.create("div", "legend");
      div.innerHTML = `
        <div class="legend" style="padding:8px; border-radius:8px;">
          <div style="font-weight:600;">Map Legend</div>
          <div class="legend-row"><svg width="12" height="12"><circle cx="6" cy="6" r="6" fill="#0074D9"/></svg><span style="margin-left:8px;">Hospitals</span></div>
          <div class="legend-row"><svg width="12" height="12"><circle cx="6" cy="6" r="6" fill="#EF4444"/></svg><span style="margin-left:8px;">Blood Banks</span></div>
        </div>`;
      return div;
    };
    legend.addTo(map);

    // ===== Chatbot logic =====
    const chatbot = document.getElementById('chatbot');
    const chatBody = document.getElementById('chat-body');
    const userInput = document.getElementById('user-input');

    function toggleChatbot(){ chatbot.style.display = chatbot.style.display==='flex' ? 'none' : 'flex'; }

    function addMessage(msg, sender){
      const div = document.createElement('div');
      div.classList.add('chat-msg', sender==='bot' ? 'bot-msg' : 'user-msg');
      div.textContent = msg;
      chatBody.appendChild(div);
      chatBody.scrollTop = chatBody.scrollHeight;
    }

    function sendMessage(){
      const raw = userInput.value.trim();
      if(!raw) return;
      addMessage(raw, 'user');
      userInput.value = '';
      // small delay to simulate thinking
      setTimeout(()=> botReply(raw), 400);
    }

    // Helper: haversine distance (km)
    function haversine(lat1, lon1, lat2, lon2){
      function toRad(x){ return x * Math.PI / 180; }
      const R = 6371;
      const dLat = toRad(lat2 - lat1);
      const dLon = toRad(lon2 - lon1);
      const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
                Math.sin(dLon/2) * Math.sin(dLon/2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
      return R * c;
    }

    // Find nearest blood bank from coordinates
    function findNearestBloodBank(lat, lon){
      let min = Infinity;
      let nearest = null;
      bloodBanks.forEach(b => {
        const d = haversine(lat, lon, b.lat, b.lon);
        if(d < min){ min = d; nearest = { bank: b, distance: d }; }
      });
      return nearest;
    }

    // Find blood banks that have a blood group
    function banksWithGroup(group){
      const normalized = group.toUpperCase().replace(/\s+/g,'');
      return bloodBanks.filter(b => b.groups.map(g=>g.toUpperCase()).includes(normalized));
    }

    // Ask for geolocation and then reply with nearest blood bank
    function replyWithNearestBloodBank(){
      if(!navigator.geolocation){
        addMessage("Geolocation not available in your browser. Please type a city or landmark.", 'bot');
        return;
      }
      addMessage("Requesting your location to find the nearest blood bank... (please allow location access)", 'bot');
      navigator.geolocation.getCurrentPosition(pos => {
        const lat = pos.coords.latitude;
        const lon = pos.coords.longitude;
        const nearest = findNearestBloodBank(lat, lon);
        if(!nearest){
          addMessage("No blood banks found in our data.", 'bot');
          return;
        }
        const b = nearest.bank;
        const dKm = nearest.distance.toFixed(2);
        addMessage(`Nearest blood bank: ${b.name} (${b.location}) — approx ${dKm} km away. Available: ${b.groups.join(", ")}`, 'bot');
        // center map & open popup
        map.setView([b.lat, b.lon], 14, { animate: true });
        bloodBankMarkers[b.id].openPopup();
      }, err => {
        addMessage("Location access denied or failed. Please allow location access or ask for blood bank by name.", 'bot');
      }, { enableHighAccuracy: true, timeout: 10000, maximumAge: 60000 });
    }

    // Main bot reply handling (supports blood bank queries)
    function botReply(rawInput){
      const input = (rawInput || '').toLowerCase();

      // quick greetings
      if (input.includes("hello") || input.includes("hi") || input.includes("hey")) {
        addMessage("Hi there! 👋 How can I assist you? Try 'Nearest blood bank' or 'Is O+ available?'", 'bot');
        return;
      }

      // nearest blood bank
      if (input.includes("nearest blood") || input.includes("nearest blood bank") || input.includes("closest blood")) {
        replyWithNearestBloodBank();
        return;
      }

      // availability for specific blood group e.g., "is o+ available" or "availability of a-"
      const groupMatch = rawInput.match(/(a\+|a-|b\+|b-|o\+|o-|ab\+|ab-)/i);
      if(groupMatch){
        const group = groupMatch[0].toUpperCase();
        const banks = banksWithGroup(group);
        if(banks.length === 0){
          addMessage(`No blood banks in our list currently show availability of ${group}. Try checking the map or contact nearby banks.`, 'bot');
        } else {
          const names = banks.map(b => `${b.name} (${b.location})`).join('; ');
          addMessage(`${group} is currently listed at: ${names}. Click a bank in the table to view details on the map.`, 'bot');
          // optionally open the first bank on map
          const first = banks[0];
          map.setView([first.lat, first.lon], 14, { animate: true });
          bloodBankMarkers[first.id].openPopup();
        }
        return;
      }

      // general bed availability
      if (input.includes("bed") || input.includes("availability")) {
        addMessage("You can check real-time bed status on the live map or in the dashboard table. Ask 'nearest hospital' or 'emergency' for more.", 'bot');
        return;
      }

      if (input.includes("emergency")) {
        addMessage("In an emergency, AIIMS and Safdarjung hospitals maintain 24x7 emergency wards. Use the map to get directions and contact info.", 'bot');
        return;
      }

      if (input.includes("icu")) {
        addMessage("ICU beds are reported at Fortis and Max Healthcare in our demo data. Use the map for contact details.", 'bot');
        return;
      }

      if (input.includes("nearest hospital") || input.includes("nearby hospital")) {
        // use geolocation to find nearest hospital
        if(!navigator.geolocation){
          addMessage("Geolocation unavailable. Please search the map or type a city.", 'bot');
          return;
        }
        addMessage("Requesting your location to find the nearest hospital... (please allow)", 'bot');
        navigator.geolocation.getCurrentPosition(pos => {
          const lat = pos.coords.latitude; const lon = pos.coords.longitude;
          let min = Infinity; let nearest = null;
          hospitals.forEach(h => {
            const d = haversine(lat, lon, h.lat, h.lon);
            if(d < min){ min = d; nearest = { h, d }; }
          });
          if(nearest){
            addMessage(`Nearest hospital: ${nearest.h.name} (~${nearest.d.toFixed(2)} km). Contact: ${nearest.h.contact}`, 'bot');
            map.setView([nearest.h.lat, nearest.h.lon], 14, { animate: true });
            hospitalMarkers[nearest.h.id].openPopup();
          } else {
            addMessage("No hospitals found in our dataset.", 'bot');
          }
        }, err => {
          addMessage("Location denied or failed. Please allow location access or search manually.", 'bot');
        }, { enableHighAccuracy: true, timeout:10000 });
        return;
      }

      if (input.includes("thanks") || input.includes("thank you")) {
        addMessage("You're welcome! Wishing you good health 💙", 'bot');
        return;
      }

      // fallback
      addMessage("I didn't quite catch that. Try: 'Nearest blood bank', 'Is O+ available?', 'Nearest hospital', or 'Emergency'.", 'bot');
    }

    // Support pressing Enter in input
    userInput.addEventListener('keydown', function(e){
      if(e.key === 'Enter'){ sendMessage(); }
    });

    // expose sendMessage for button
    window.sendMessage = sendMessage;
    window.toggleChatbot = toggleChatbot;

  </script>

</body>
</html>


