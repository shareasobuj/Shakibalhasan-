# Shakibalhasan
<!--
Single-file React + Bootstrap website about Shakib Al Hasan.
How to use:
1. Save this file as `index.html`.
2. Open in browser (or host on GitHub Pages). It uses browser-built React (CDN) and Babel to compile JSX in-browser.
3. Sign up an admin account (Signup page) then Login to access Admin Panel (Edit pages, upload images). All data is stored in localStorage (local API simulation).

This file includes:
- 10 pages (Home, Biography, Stats, Records, Gallery, Timeline, Achievements, Media, Fan Zone, Contact)
- Signup & Login pages (local API using localStorage)
- Admin area to edit text & images; images saved as base64 in localStorage
- Simple hash-based routing (no build step)

Note: For production you should move the API to a real backend and restrict admin routes properly.
-->


<html lang="bn">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Shakib Al Hasan - World Class Allrounder</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <style>
    body { padding-top: 70px; }
    .hero { background: linear-gradient(90deg, #0d6efd22, #6c757d11); padding: 40px 0; }
    .page-content { min-height: 60vh; }
    .admin-badge { position: absolute; right: 1rem; top: 70px; }
    .thumb { max-width: 120px; max-height: 90px; object-fit: cover; margin: 6px; }
  </style>
</head>
<body>

  <div id="root"></div>

  <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
  <script type="text/babel">

// --- Simple local API using localStorage ---
const LOCAL_KEY = 'shakib_site_v1';
const USERS_KEY = 'shakib_site_users_v1';

const defaultContent = {
  title: 'সাকিব আল হাসান — বিশ্বের সেরা অলরাউন্ডার',
  pages: {
    home: {
      title: 'হোম',
      hero: 'সাকিব আল হাসান — বাংলাদেশের গর্ব, বিশ্ব মানের অলরাউন্ডার।',
      content: '<p>সাকিব আল হাসান বর্তমানে বাংলাদেশের টেস্ট, ওয়ানডে ও টি২০ দলের অন্যতম প্রধান ক্রিকেটার। একজন বিশ্বমানের অলরাউন্ডার হিসেবে শাকিব ব্যাটিং ও বোলিং—দুই দিকেই উজ্জ্বল পারফরম্যান্স দেখিয়েছেন।</p>'
    },
    biography: {
      title: 'জীবনী',
      content: `
        <h3>প্রাথমিক জীবন</h3>
        <p>সাকিব আল হাসান ০৭ মার্চ ১৯৮৭ সালে জন্মগ্রহণ করেন। তিনি ডান-হাতি ব্যাটসম্যান এবং বাঁহাতি স্পিন অলরাউন্ডার।</p>
        <h3>ক্যারিয়ার</h3>
        <p>ভাল পারফরম্যান্সের জন্য আন্তর্জাতিক ক্রিকেটে পরিচিতি পান। ওয়ানডে, টেস্ট এবং টি২০-তে গুরুত্বপূর্ণ সাফল্য অর্জন করেছেন।</p>
      `
    },
    stats: {
      title: 'পরিসংখ্যান',
      content: `
        <h4>সংক্ষেপ পরিসংখ্যান (উদাহরণ)</h4>
        <ul>
          <li>টেস্ট ম্যাচ: ৬৫+  (উদাহরণ)</li>
          <li>ওয়ানডে: ২২০+ (উদাহরণ)</li>
          <li>টি২০: ৯০+ (উদাহরণ)</li>
          <li>সর্বোচ্চ স্কোর, সেরা বোলিং ফিগার ইত্যাদি ডাইনামিকলি এড করতে পারবেন।</li>
        </ul>
      `
    },
    records: { title: 'রেকর্ডস', content: '<p>বিশ্ব রেকর্ড এবং দেশের রেকর্ড সমূহ এখানে তালিকাভুক্ত থাকবে।</p>' },
    gallery: { title: 'গ্যালারি', content: '<p>ছবি ও ভিডিও আপলোড করে গ্যালারি সাজাতে পারবেন।</p>', images: [] },
    timeline: { title: 'টাইমলাইন', content: '<p>ক্যারিয়ারের গুরুত্বপূর্ণ মুহূর্তগুলো টাইমলাইন আকারে দেখানো হবে।</p>' },
    achievements: { title: 'অর্জন', content: '<p>সাকিবের অর্জনসমুহ: প্লেয়ার অফ দ্য টুর্নামেন্ট, সেরা অলরাউন্ডার অ্যাওয়ার্ড ইত্যাদি।</p>' },
    media: { title: 'মিডিয়া', content: '<p>ইন্টারভিউ, ভিডিও ও সংবাদপত্র আর্কাইভ।</p>' },
    fan: { title: 'ফ্যান জোন', content: '<p>ফ্যান কমেন্টস, মেমস এবং ইন্টারঅ্যাকটিভ কনটেন্ট।</p>' },
    contact: { title: 'যোগাযোগ', content: '<p>কন্ট্যাক্ট ফর্ম ও সামাজিক লিংক।</p>' }
  }
};

function getSiteData(){
  const raw = localStorage.getItem(LOCAL_KEY);
  if(!raw){
    localStorage.setItem(LOCAL_KEY, JSON.stringify(defaultContent));
    return defaultContent;
  }
  try{ return JSON.parse(raw); }
  catch(e){ localStorage.setItem(LOCAL_KEY, JSON.stringify(defaultContent)); return defaultContent; }
}

function saveSiteData(data){
  localStorage.setItem(LOCAL_KEY, JSON.stringify(data));
}

const api = {
  signup: ({username, password}) => {
    const usersRaw = localStorage.getItem(USERS_KEY);
    const users = usersRaw ? JSON.parse(usersRaw) : [];
    if(users.find(u=>u.username===username)) throw new Error('User exists');
    users.push({username, password});
    localStorage.setItem(USERS_KEY, JSON.stringify(users));
    return {ok:true};
  },
  login: ({username,password}) => {
    const usersRaw = localStorage.getItem(USERS_KEY) || '[]';
    const users = JSON.parse(usersRaw);
    const u = users.find(x=>x.username===username && x.password===password);
    if(!u) throw new Error('Invalid credentials');
    // create simple token
    const token = btoa(username+':'+Date.now());
    localStorage.setItem('shakib_token', token);
    localStorage.setItem('shakib_user', username);
    return {token, username};
  },
  logout: () => {
    localStorage.removeItem('shakib_token');
    localStorage.removeItem('shakib_user');
  },
  whoami: () => localStorage.getItem('shakib_user') || null,
  getContent: () => getSiteData(),
  saveContent: (newData) => { saveSiteData(newData); return {ok:true}; },
  uploadImage: async (file) => {
    return new Promise((res,rej)=>{
      const reader = new FileReader();
      reader.onload = function(e){
        const dataUrl = e.target.result;
        const data = getSiteData();
        data.pages.gallery.images = data.pages.gallery.images || [];
        data.pages.gallery.images.push({id:Date.now(), src:dataUrl, name:file.name});
        saveSiteData(data);
        res({ok:true, src:dataUrl});
      }
      reader.onerror = rej;
      reader.readAsDataURL(file);
    });
  }
};

// --- Simple Hash Router ---
const pagesOrder = ['home','biography','stats','records','gallery','timeline','achievements','media','fan','contact'];
function useHashRoute(){
  const [route, setRoute] = React.useState(location.hash.replace('#','') || 'home');
  React.useEffect(()=>{
    const onHash=()=> setRoute(location.hash.replace('#','') || 'home');
    window.addEventListener('hashchange', onHash);
    return ()=> window.removeEventListener('hashchange', onHash);
  },[]);
  return [route, (r)=> location.hash = '#'+r];
}

function Nav({current, onNavigate, isAdmin, onLogout}){
  return (
    <nav className="navbar navbar-expand-lg navbar-dark bg-dark fixed-top">
      <div className="container">
        <a className="navbar-brand" href="#home">Shakib Al Hasan</a>
        <button className="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navmenu">
          <span className="navbar-toggler-icon"></span>
        </button>
        <div className="collapse navbar-collapse" id="navmenu">
          <ul className="navbar-nav me-auto mb-2 mb-lg-0">
            {pagesOrder.map(p=> (
              <li key={p} className="nav-item">
                <a className={"nav-link "+(current===p? 'active':'')} href={'#'+p}>{getSiteData().pages[p].title}</a>
              </li>
            ))}
          </ul>
          <div className="d-flex">
            {!api.whoami() && <a className="btn btn-outline-light me-2" href="#login">Admin Login</a>}
            {!api.whoami() && <a className="btn btn-primary" href="#signup">Sign Up</a>}
            {api.whoami() && <>
              <span className="navbar-text text-white me-2">{api.whoami()}</span>
              <a className="btn btn-warning me-2" href="#admin">Admin</a>
              <button className="btn btn-outline-light" onClick={()=>{api.logout(); location.reload();}}>Logout</button>
            </>}
          </div>
        </div>
      </div>
    </nav>
  );
}

function Home({data}){
  const p = data.pages.home;
  return (
    <div className="container page-content">
      <div className="hero rounded-3 p-4 mb-4">
        <div className="row align-items-center">
          <div className="col-md-8">
            <h1>{data.title}</h1>
            <p dangerouslySetInnerHTML={{__html:p.hero}} />
          </div>
          <div className="col-md-4 text-center">
            <img src={data.pages.gallery.images && data.pages.gallery.images[0] ? data.pages.gallery.images[0].src : 'https://via.placeholder.com/300x200?text=Shakib'} className="img-fluid rounded" alt="Shakib" />
          </div>
        </div>
      </div>
      <div dangerouslySetInnerHTML={{__html:p.content}} />
    </div>
  );
}

function GenericPage({pageKey, data}){
  const p = data.pages[pageKey];
  return (
    <div className="container page-content">
      <h2>{p.title}</h2>
      <div dangerouslySetInnerHTML={{__html:p.content}} />
    </div>
  );
}

function Gallery({data, refresh}){
  const imgs = data.pages.gallery.images || [];
  return (
    <div className="container page-content">
      <h2>গ্যালারি</h2>
      <p>আপলোড করা ছবি</p>
      <div className="d-flex flex-wrap">
        {imgs.length===0 && <div className="text-muted">কোনো ছবি নেই — Admin হিসেবে লগইন করে আপলোড করুন।</div>}
        {imgs.map(img=> (
          <img key={img.id} src={img.src} className="thumb rounded" alt={img.name} />
        ))}
      </div>
    </div>
  );
}

function Signup(){
  const [u, setU] = React.useState('');
  const [p, setP] = React.useState('');
  const [msg, setMsg] = React.useState(null);
  const onSubmit = (e)=>{
    e.preventDefault();
    try{ api.signup({username:u,password:p}); setMsg({type:'success', text:'Signup successful. Please login.'}); location.hash='#login'; }
    catch(err){ setMsg({type:'danger', text:err.message}); }
  }
  return (
    <div className="container page-content">
      <h2>Admin Sign Up</h2>
      {msg && <div className={`alert alert-${msg.type}`}>{msg.text}</div>}
      <form onSubmit={onSubmit} className="col-md-6">
        <div className="mb-3"><label className="form-label">Username</label><input className="form-control" value={u} onChange={e=>setU(e.target.value)} required /></div>
        <div className="mb-3"><label className="form-label">Password</label><input type="password" className="form-control" value={p} onChange={e=>setP(e.target.value)} required /></div>
        <button className="btn btn-primary">Sign Up</button>
      </form>
    </div>
  );
}

function Login(){
  const [u, setU] = React.useState('');
  const [p, setP] = React.useState('');
  const [msg, setMsg] = React.useState(null);
  const onSubmit = (e)=>{
    e.preventDefault();
    try{ api.login({username:u,password:p}); setMsg({type:'success', text:'Logged in'}); location.hash='#admin'; location.reload(); }
    catch(err){ setMsg({type:'danger', text:err.message}); }
  }
  return (
    <div className="container page-content">
      <h2>Admin Login</h2>
      {msg && <div className={`alert alert-${msg.type}`}>{msg.text}</div>}
      <form onSubmit={onSubmit} className="col-md-6">
        <div className="mb-3"><label className="form-label">Username</label><input className="form-control" value={u} onChange={e=>setU(e.target.value)} required /></div>
        <div className="mb-3"><label className="form-label">Password</label><input type="password" className="form-control" value={p} onChange={e=>setP(e.target.value)} required /></div>
        <button className="btn btn-primary">Login</button>
      </form>
    </div>
  );
}

function Admin({data, onChange}){
  const [selected, setSelected] = React.useState('home');
  const [draft, setDraft] = React.useState('');
  const [msg, setMsg] = React.useState(null);
  React.useEffect(()=>{ setDraft(data.pages[selected].content || ''); }, [selected]);

  const save = ()=>{
    const copy = JSON.parse(JSON.stringify(data));
    copy.pages[selected].content = draft;
    saveSiteData(copy);
    onChange(copy);
    setMsg({type:'success', text:'Saved.'});
    setTimeout(()=>setMsg(null),2000);
  }

  const upload = async (e)=>{
    const file = e.target.files[0];
    if(!file) return;
    await api.uploadImage(file);
    const updated = getSiteData();
    onChange(updated);
  }

  const saveTitle = (e)=>{
    const copy = JSON.parse(JSON.stringify(data));
    copy.title = e.target.value;
    saveSiteData(copy);
    onChange(copy);
  }

  return (
    <div className="container page-content position-relative">
      <h2>Admin Panel</h2>
      <div className="mb-3">
        <label className="form-label">Site Title</label>
        <input className="form-control" defaultValue={data.title} onBlur={saveTitle} />
      </div>
      <div className="row">
        <div className="col-md-3">
          <h5>Pages</h5>
          <ul className="list-group">
            {Object.keys(data.pages).map(k=> (
              <li key={k} className={"list-group-item "+(k===selected? 'active':'')} style={{cursor:'pointer'}} onClick={()=>setSelected(k)}>{data.pages[k].title} <small className="text-muted">({k})</small></li>
            ))}
          </ul>
          <hr />
          <h6>Gallery</h6>
          <input type="file" className="form-control" onChange={upload} />
        </div>
        <div className="col-md-9">
          <h5>Editing: {selected} — {data.pages[selected].title}</h5>
          <div className="mb-2">
            <label className="form-label">Page Title</label>
            <input className="form-control" defaultValue={data.pages[selected].title} onBlur={(e)=>{ const copy = JSON.parse(JSON.stringify(data)); copy.pages[selected].title = e.target.value; saveSiteData(copy); onChange(copy); }} />
          </div>
          <div className="mb-2">
            <label className="form-label">HTML Content</label>
            <textarea className="form-control" rows={12} value={draft} onChange={e=>setDraft(e.target.value)} />
          </div>
          <button className="btn btn-success me-2" onClick={save}>Save Changes</button>
          <button className="btn btn-secondary" onClick={()=>{ setDraft(data.pages[selected].content || ''); }}>Reset</button>
          {msg && <div className={`alert alert-${msg.type} mt-2`}>{msg.text}</div>}
          <hr />
          <h6>Current Gallery</h6>
          <div className="d-flex flex-wrap">
            {(data.pages.gallery.images||[]).map(img=> (
              <div key={img.id} className="me-2 text-center">
                <img src={img.src} className="thumb rounded" alt={img.name} /><br/>
                <small className="text-muted">{img.name}</small>
              </div>
            ))}
          </div>
        </div>
      </div>
    </div>
  );
}

function App(){
  const [route] = useHashRoute();
  const [data, setData] = React.useState(getSiteData());

  React.useEffect(()=>{
    // reload data when storage changes (e.g., from admin changes)
    const onStorage = (e)=>{ if(e.key===LOCAL_KEY) setData(getSiteData()); }
    window.addEventListener('storage', onStorage);
    return ()=> window.removeEventListener('storage', onStorage);
  },[]);

  return (
    <div>
      <Nav current={route} />
      <main>
        {route==='home' && <Home data={data} />}
        {route==='biography' && <GenericPage pageKey={'biography'} data={data} />}
        {route==='stats' && <GenericPage pageKey={'stats'} data={data} />}
        {route==='records' && <GenericPage pageKey={'records'} data={data} />}
        {route==='gallery' && <Gallery data={data} />}
        {route==='timeline' && <GenericPage pageKey={'timeline'} data={data} />}
        {route==='achievements' && <GenericPage pageKey={'achievements'} data={data} />}
        {route==='media' && <GenericPage pageKey={'media'} data={data} />}
        {route==='fan' && <GenericPage pageKey={'fan'} data={data} />}
        {route==='contact' && <GenericPage pageKey={'contact'} data={data} />}
        {route==='signup' && <Signup />}
        {route==='login' && <Login />}
        {route==='admin' && (api.whoami() ? <Admin data={data} onChange={setData} /> : <div className="container page-content"><div className="alert alert-warning">প্রবেশাধিকার নেই — প্রথমে লগইন করুন।</div></div>)}
      </main>
      <footer className="bg-dark text-white py-4 mt-4">
        <div className="container text-center">
          <div>© {new Date().getFullYear()} Shakib Al Hasan Fan Site — Built with React & Bootstrap</div>
        </div>
      </footer>
    </div>
  );
}

ReactDOM.createRoot(document.getElementById('root')).render(<App />);

  </script>
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>

