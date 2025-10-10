<DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  | Hospital Bed Tracker</title>

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
    body { background: var(--bg-light); }

    nav {
      background: var(--white);
      padding:1rem 2rem;
      display:flex;
      justify-content:space-between;
      align-items:center;
      box-shadow:0 2px 6px rgba(0,0,0,0.1);
      position: sticky;
      top:0;
      z-index:1000;
    }
    nav h1 { color: var(--primary-color); font-weight:700; font-size:1.6rem; }
    nav ul { list-style:none; display:flex; gap:1.5rem; }
    nav ul li a { text-decoration:none; color: var(--text-dark); font-weight:500; cursor:pointer;}
    nav ul li a:hover { color: var(--primary-color); }

    section { padding: 4rem 2rem; }
    h2.section-title { text-align:center; font-size:2.2rem; font-weight:700; margin-bottom:2rem; color:var(--text-dark); }

    /* Hero */
    .hero { text-align:center; padding:6rem 2rem; background: linear-gradient(to right,#E0F2FE,#F0FDFA);}
    .hero h2 { font-size:2.6rem; font-weight:700; color:var(--text-dark); }
    .hero p { max-width:700px; margin:1rem auto; color:var(--text-gray); font-size:1.1rem; }

    .hero button { background: var(--primary-color); color:white; padding:0.8rem 2rem; border:none; border-radius:8px; cursor:pointer; font-weight:600; }

    /* Features */
    .features-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(250px,1fr)); gap:2rem; max-width:1200px; margin:0 auto;}
    .feature-card { background: var(--white); padding:2rem; border-radius:12px; box-shadow:0 6px 20px rgba(0,0,0,0.05); text-align:center; transition: transform 0.3s ease, box-shadow 0.3s ease;}
    .feature-card:hover { transform:translateY(-8px); box-shadow:0 10px 30px rgba(0,0,0,0.1);}
    .feature-icon { width:60px; height:60px; margin-bottom:1rem; }
    .feature-title { font-size:1.3rem; font-weight:600; margin-bottom:0.6rem; color: var(--primary-color);}
    .feature-desc { font-size:1rem; color:var(--text-gray); }

    /* Dashboard Table */
    .dashboard table { width:100%; max-width:1100px; margin:0 auto; border-collapse:collapse; box-shadow:0 4px 20px rgba(0,0,0,0.05);}
    th,td { padding:1rem; text-align:center; border-bottom:1px solid #E5E7EB;}
    th { background: var(--primary-color); color:white; font-weight:600;}
    tr:hover { background:#F3F4F6; transition:0.3s;}
    .status { padding:0.4rem 0.8rem; border-radius:6px; font-weight:600; display:inline-block;}
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
    #map { width:100%; height:70vh; border-radius:12px; box-shadow:0 4px 12px rgba(0,0,0,0.1); margin:2rem 0; }

    /* Floating Chatbot */
    .chatbot-toggle { position:fixed; bottom:20px; right:20px; background:var(--secondary-color); color:white; border:none; border-radius:50%; width:60px; height:60px; font-size:1.8rem; cursor:pointer; z-index:1001; box-shadow:0 4px 15px rgba(0,0,0,0.2);}
    .chatbot-container { position:fixed; bottom:90px; right:20px; width:340px; height:420px; background: var(--white); border-radius:16px; box-shadow:0 6px 25px rgba(0,0,0,0.3); display:none; flex-direction:column; overflow:hidden; z-index:1002;}
    .chat-header { background: var(--primary-color); color:white; text-align:center; padding:1rem; font-weight:600; }
    .chat-body { flex:1; padding:0.8rem; overflow-y:auto; display:flex; flex-direction:column; gap:0.5rem;}
    .chat-msg { padding:0.6rem 1rem; border-radius:12px; max-width:80%; }
    .bot-msg { background:#E0F2FE; align-self:flex-start; }
    .user-msg { background:#DCFCE7; align-self:flex-end; }
    .chat-input { display:flex; border-top:1px solid #E5E7EB; }
    .chat-input input { flex:1; border:none; padding:0.8rem; outline:none; }
    .chat-input button { border:none; background: var(--primary-color); color:white; padding:0 1.2rem; cursor:pointer; }

    footer { background: var(--white); text-align:center; padding:1.5rem; margin-top:2rem; box-shadow:0 -2px 6px rgba(0,0,0,0.1);}
    footer p { color:#4B5563; font-size:0.95rem; }

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
        <img src="https://cdn-icons-png.flaticon.com/512/2966/2966327.png" class="feature-icon" />
        <h3 class="feature-title">Real-Time Availability</h3>
        <p class="feature-desc">Instant bed updates across all wards.</p>
      </div>
      <div class="feature-card">
        <img src="https://cdn-icons-png.flaticon.com/512/483/483356.png" class="feature-icon" />
        <h3 class="feature-title">Search & Filter</h3>
        <p class="feature-desc">Find hospitals by name, ward, or availability.</p>
      </div>
      <div class="feature-card">
        <img src="https://cdn-icons-png.flaticon.com/512/942/942799.png" class="feature-icon" />
        <h3 class="feature-title">Admin Dashboard</h3>
        <p class="feature-desc">Securely update bed records.</p>
      </div>
      <div class="feature-card">
        <img src="https://cdn-icons-png.flaticon.com/512/190/190411.png" class="feature-icon" />
        <h3 class="feature-title">Smart Alerts</h3>
        <p class="feature-desc">Color-coded bed availability status.</p>
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
  <!-- FAQ Section -->
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

  <div class="faq-item">
    <div class="faq-question">Is this service available in my city?</div>
    <div class="faq-answer">We are actively expanding our network of partner hospitals. Currently, we are operational in Delhi. Sign up to be notified when we launch in your city.</div>
  </div>
</section>

  <!-- Login -->
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

<script>
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
</script>


  <!-- Map -->
  <section id="map-section">
    <h2 style="text-align:center;">🏥 Hospitals in Delhi</h2>
    <div id="map"></div>
  </section>

  <!-- Chatbot -->
  <button class="chatbot-toggle" onclick="toggleChatbot()">💬</button>
  <div class="chatbot-container" id="chatbot">
    <div class="chat-header">🩺 BedMatrix Assistant</div>
    <div class="chat-body" id="chat-body">
      <div class="chat-msg bot-msg">Hello! 👋 I’m your BedMatrix assistant. How can I help you?</div>
    </div>
    <div class="chat-input">
      <input type="text" id="user-input" placeholder="Type your message..." />
      <button onclick="sendMessage()">Send</button>
    </div>
  </div>

  <footer id="contact">
    <p>©️ 2025 BedMatrix | Smarter Healthcare for Everyone</p>
  </footer>

  <!-- Leaflet JS -->
  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
  <script>
    // Smooth FAQ toggle
    document.querySelectorAll('.faq-question').forEach(q=>{
      q.addEventListener('click',()=>{ 
        const ans = q.nextElementSibling;
        ans.style.display = ans.style.display==='block'?'none':'block';
      });
    });

    // ===== Map =====
    const map = L.map('map').setView([28.6139,77.2090],12);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{maxZoom:19, attribution:'&copy; OpenStreetMap contributors'}).addTo(map);
    const hospitals = [
      {name:"AIIMS Hospital", lat:28.5672, lon:77.2100},
      {name:"Fortis Hospital", lat:28.5562, lon:77.1000},
      {name:"Safdarjung Hospital", lat:28.5675, lon:77.2097},
      {name:"Max Healthcare", lat:28.6280, lon:77.2180}
    ];
    hospitals.forEach(h=>L.marker([h.lat,h.lon]).addTo(map).bindPopup(`<b>${h.name}</b><br>Delhi`));

    // ===== Chatbot =====
    const chatbot = document.getElementById('chatbot');
    const chatBody = document.getElementById('chat-body');
    const userInput = document.getElementById('user-input');

    function toggleChatbot(){ chatbot.style.display = chatbot.style.display==='flex'?'none':'flex'; }
    function sendMessage(){
      const msg = userInput.value.trim();
      if(!msg) return;
      addMessage(msg,'user'); userInput.value=''; setTimeout(()=> botReply(msg),500);
    }
    function addMessage(msg,sender){
      const div = document.createElement('div');
      div.classList.add('chat-msg', sender==='bot'?'bot-msg':'user-msg');
      div.textContent = msg;
      chatBody.appendChild(div);
      chatBody.scrollTop = chatBody.scrollHeight;
    }
    function botReply(input){
      function botReply(input) {
  input = input.toLowerCase();
  let reply = "I'm not sure I understand. Could you please clarify?";

  if (input.includes("hello") || input.includes("hi")) {
    reply = "Hi there! 👋 How can I assist you in finding hospital beds today?";
  } 
  else if (input.includes("bed") || input.includes("availability")) {
    reply = "You can check real-time bed status directly on the live map or dashboard.";
  } 
  else if (input.includes("emergency")) {
    reply = "In an emergency, please visit AIIMS or Safdarjung Hospital — both have 24x7 emergency wards.";
  } 
  else if (input.includes("icu")) {
    reply = "ICU beds are currently available at Fortis and Max Healthcare.";
  } 
  else if (input.includes("location") || input.includes("nearest")) {
    reply = "Please enable location access — I’ll suggest hospitals nearest to you.";
  } 
  else if (input.includes("nearest hospital") || input.includes("nearby hospital")) {
    reply = "Here are some nearby hospitals: AIIMS, Fortis, Safdarjung, Max Healthcare. Check the map for exact locations.";
  } 
  else if (input.includes("nearest icu")) {
    reply = "Nearest ICU facilities are available at AIIMS, Fortis, and Max Healthcare.";
  } 
  else if (input.includes("beds at") || input.includes("how many beds")) {
    reply = "Please specify the hospital name. For example, 'How many beds at AIIMS?'";
  } 
  else if (input.includes("thanks") || input.includes("thank you")) {
    reply = "You're most welcome! Wishing you good health 💙";
  }

  addMessage(reply, 'bot');
}

    }
  </script>
</body>
</html>
