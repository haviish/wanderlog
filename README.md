[wanderlog_web_app_for_custom_domain.html](https://github.com/user-attachments/files/33515620/wanderlog_web_app_for_custom_domain.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>WanderLog - National Parks, States & Travel Passport</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      background-color: #020617;
      color: #f8fafc;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
      margin: 0;
      padding: 0;
    }
    ::-webkit-scrollbar { width: 6px; height: 6px; }
    ::-webkit-scrollbar-track { background: #0f172a; }
    ::-webkit-scrollbar-thumb { background: #334155; border-radius: 9999px; }
    ::-webkit-scrollbar-thumb:hover { background: #475569; }
  </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen">
  <div id="app" class="min-h-screen flex flex-col">
    <div class="min-h-screen flex flex-col items-center justify-center p-6 space-y-4 text-center">
      <div class="w-12 h-12 rounded-2xl bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 flex items-center justify-center text-2xl animate-pulse">
        🧭
      </div>
      <div class="text-sm font-bold text-slate-300">Loading WanderLog...</div>
    </div>
  </div>

  <div id="toast-container" class="fixed bottom-4 right-4 z-50 flex flex-col gap-2 pointer-events-none"></div>

  <script>
    const US_STATES = [
      { id: 'AL', name: 'Alabama', region: 'South' }, { id: 'AK', name: 'Alaska', region: 'West' },
      { id: 'AZ', name: 'Arizona', region: 'West' }, { id: 'AR', name: 'Arkansas', region: 'South' },
      { id: 'CA', name: 'California', region: 'West' }, { id: 'CO', name: 'Colorado', region: 'West' },
      { id: 'CT', name: 'Connecticut', region: 'Northeast' }, { id: 'DE', name: 'Delaware', region: 'Northeast' },
      { id: 'FL', name: 'Florida', region: 'South' }, { id: 'GA', name: 'Georgia', region: 'South' },
      { id: 'HI', name: 'Hawaii', region: 'West' }, { id: 'ID', name: 'Idaho', region: 'West' },
      { id: 'IL', name: 'Illinois', region: 'Midwest' }, { id: 'IN', name: 'Indiana', region: 'Midwest' },
      { id: 'IA', name: 'Iowa', region: 'Midwest' }, { id: 'KS', name: 'Kansas', region: 'Midwest' },
      { id: 'KY', name: 'Kentucky', region: 'South' }, { id: 'LA', name: 'Louisiana', region: 'South' },
      { id: 'ME', name: 'Maine', region: 'Northeast' }, { id: 'MD', name: 'Maryland', region: 'Northeast' },
      { id: 'MA', name: 'Massachusetts', region: 'Northeast' }, { id: 'MI', name: 'Michigan', region: 'Midwest' },
      { id: 'MN', name: 'Minnesota', region: 'Midwest' }, { id: 'MS', name: 'Mississippi', region: 'South' },
      { id: 'MO', name: 'Missouri', region: 'Midwest' }, { id: 'MT', name: 'Montana', region: 'West' },
      { id: 'NE', name: 'Nebraska', region: 'Midwest' }, { id: 'NV', name: 'Nevada', region: 'West' },
      { id: 'NH', name: 'New Hampshire', region: 'Northeast' }, { id: 'NJ', name: 'New Jersey', region: 'Northeast' },
      { id: 'NM', name: 'New Mexico', region: 'West' }, { id: 'NY', name: 'New York', region: 'Northeast' },
      { id: 'NC', name: 'North Carolina', region: 'South' }, { id: 'ND', name: 'North Dakota', region: 'Midwest' },
      { id: 'OH', name: 'Ohio', region: 'Midwest' }, { id: 'OK', name: 'Oklahoma', region: 'South' },
      { id: 'OR', name: 'Oregon', region: 'West' }, { id: 'PA', name: 'Pennsylvania', region: 'Northeast' },
      { id: 'RI', name: 'Rhode Island', region: 'Northeast' }, { id: 'SC', name: 'South Carolina', region: 'South' },
      { id: 'SD', name: 'South Dakota', region: 'Midwest' }, { id: 'TN', name: 'Tennessee', region: 'South' },
      { id: 'TX', name: 'Texas', region: 'South' }, { id: 'UT', name: 'Utah', region: 'West' },
      { id: 'VT', name: 'Vermont', region: 'Northeast' }, { id: 'VA', name: 'Virginia', region: 'South' },
      { id: 'WA', name: 'Washington', region: 'West' }, { id: 'WV', name: 'West Virginia', region: 'South' },
      { id: 'WI', name: 'Wisconsin', region: 'Midwest' }, { id: 'WY', name: 'Wyoming', region: 'West' },
      { id: 'DC', name: 'Washington D.C.', region: 'South' }
    ];

    const MAP_GRID = [
      { id: 'AK', r: 0, c: 0 }, { id: 'ME', r: 0, c: 11 },
      { id: 'WA', r: 1, c: 1 }, { id: 'ID', r: 1, c: 2 }, { id: 'MT', r: 1, c: 3 }, { id: 'ND', r: 1, c: 4 }, { id: 'MN', r: 1, c: 5 }, { id: 'IL', r: 1, c: 6 }, { id: 'WI', r: 1, c: 7 }, { id: 'MI', r: 1, c: 8 }, { id: 'NY', r: 1, c: 9 }, { id: 'VT', r: 1, c: 10 }, { id: 'NH', r: 1, c: 11 },
      { id: 'OR', r: 2, c: 1 }, { id: 'NV', r: 2, c: 2 }, { id: 'WY', r: 2, c: 3 }, { id: 'SD', r: 2, c: 4 }, { id: 'IA', r: 2, c: 5 }, { id: 'IN', r: 2, c: 6 }, { id: 'OH', r: 2, c: 7 }, { id: 'PA', r: 2, c: 8 }, { id: 'NJ', r: 2, c: 9 }, { id: 'CT', r: 2, c: 10 }, { id: 'MA', r: 2, c: 11 },
      { id: 'CA', r: 3, c: 1 }, { id: 'UT', r: 3, c: 2 }, { id: 'CO', r: 3, c: 3 }, { id: 'NE', r: 3, c: 4 }, { id: 'MO', r: 3, c: 5 }, { id: 'KY', r: 3, c: 6 }, { id: 'WV', r: 3, c: 7 }, { id: 'VA', r: 3, c: 8 }, { id: 'MD', r: 3, c: 9 }, { id: 'DE', r: 3, c: 10 }, { id: 'RI', r: 3, c: 11 },
      { id: 'AZ', r: 4, c: 2 }, { id: 'NM', r: 4, c: 3 }, { id: 'KS', r: 4, c: 4 }, { id: 'AR', r: 4, c: 5 }, { id: 'TN', r: 4, c: 6 }, { id: 'NC', r: 4, c: 7 }, { id: 'SC', r: 4, c: 8 }, { id: 'DC', r: 4, c: 9 },
      { id: 'HI', r: 5, c: 0 }, { id: 'OK', r: 5, c: 4 }, { id: 'LA', r: 5, c: 5 }, { id: 'MS', r: 5, c: 6 }, { id: 'AL', r: 5, c: 7 }, { id: 'GA', r: 5, c: 8 },
      { id: 'TX', r: 6, c: 4 }, { id: 'FL', r: 6, c: 9 }
    ];

    const NATIONAL_PARKS = [
      { id: 'acad', name: 'Acadia', state: 'ME', established: 1919, highlight: 'Cadillac Mountain sunrise & rocky coast' },
      { id: 'nps_sam', name: 'American Samoa', state: 'HI', established: 1988, highlight: 'Paleotropical rainforests & coral reefs' },
      { id: 'arch', name: 'Arches', state: 'UT', established: 1971, highlight: 'Delicate Arch & natural red rock sandstone arches' },
      { id: 'badl', name: 'Badlands', state: 'SD', established: 1978, highlight: 'Striped clay buttes, canyons & prehistoric fossil beds' },
      { id: 'bibe', name: 'Big Bend', state: 'TX', established: 1944, highlight: 'Chisos Mountains & Rio Grande canyon vistas' },
      { id: 'bisc', name: 'Biscayne', state: 'FL', established: 1980, highlight: 'Biscayne Bay, underwater coral reef & barrier islands' },
      { id: 'blca', name: 'Black Canyon of the Gunnison', state: 'CO', established: 1999, highlight: 'Steepest, narrowest Precambrian gorge walls' },
      { id: 'brca', name: 'Bryce Canyon', state: 'UT', established: 1928, highlight: 'Dense amphitheater of red hoodoos' },
      { id: 'cany', name: 'Canyonlands', state: 'UT', established: 1964, highlight: 'Island in the Sky & Green River confluence' },
      { id: 'care', name: 'Capitol Reef', state: 'UT', established: 1971, highlight: 'Waterpocket Fold monocline & orchard oasis' },
      { id: 'cave', name: 'Carlsbad Caverns', state: 'NM', established: 1930, highlight: 'Massive limestone chambers & bat flights' },
      { id: 'chis', name: 'Channel Islands', state: 'CA', established: 1980, highlight: 'Isolation sanctuary with sea caves & island foxes' },
      { id: 'cong', name: 'Congaree', state: 'SC', established: 2003, highlight: 'Largest contiguous old-growth bottomland hardwood forest' },
      { id: 'crla', name: 'Crater Lake', state: 'OR', established: 1902, highlight: 'Deepest pure blue volcanic caldera lake in North America' },
      { id: 'cuva', name: 'Cuyahoga Valley', state: 'OH', established: 2000, highlight: 'Brandywine Falls & towpath canal trail' },
      { id: 'deva', name: 'Death Valley', state: 'CA', established: 1994, highlight: 'Badwater Basin salt flats & Zabriskie Point' },
      { id: 'dena', name: 'Denali', state: 'AK', established: 1917, highlight: 'North America’s highest peak at 20,310 feet' },
      { id: 'drto', name: 'Dry Tortugas', state: 'FL', established: 1992, highlight: 'Fort Jefferson brick masonry & isolated coral keys' },
      { id: 'ever', name: 'Everglades', state: 'FL', established: 1947, highlight: 'River of grass subtropical mangrove wilderness' },
      { id: 'gaar', name: 'Gates of the Arctic', state: 'AK', established: 1980, highlight: 'Pure trail-free Brooks Range wilderness' },
      { id: 'jega', name: 'Gateway Arch', state: 'MO', established: 2018, highlight: 'Iconic 630-foot stainless steel pioneer monument' },
      { id: 'glac', name: 'Glacier', state: 'MT', established: 1910, highlight: 'Going-to-the-Sun Road & alpine peak valleys' },
      { id: 'glba', name: 'Glacier Bay', state: 'AK', established: 1980, highlight: 'Tidewater glaciers, icebergs & humpback whales' },
      { id: 'grca', name: 'Grand Canyon', state: 'AZ', established: 1919, highlight: 'Mile-deep geological canyon wonderland' },
      { id: 'grte', name: 'Grand Teton', state: 'WY', established: 1929, highlight: 'Jagged Cathedral Group peaks & Jenny Lake' },
      { id: 'grba', name: 'Great Basin', state: 'NV', established: 1986, highlight: 'Wheeler Peak, Lehman Caves & ancient bristlecone pines' },
      { id: 'gusc', name: 'Great Sand Dunes', state: 'CO', established: 2004, highlight: 'Tallest sand dunes in North America against snowy peaks' },
      { id: 'grsm', name: 'Great Smoky Mountains', state: 'TN', established: 1934, highlight: 'Ancient mist-covered biodiversity ridgelines' },
      { id: 'gumo', name: 'Guadalupe Mountains', state: 'TX', established: 1972, highlight: 'Permian fossil reef & highest peak in Texas' },
      { id: 'hale', name: 'Haleakala', state: 'HI', established: 1961, highlight: 'Dormant volcanic summit crater & silverswords' },
      { id: 'havo', name: 'Hawaii Volcanoes', state: 'HI', established: 1916, highlight: 'Active Kilauea and Mauna Loa shield volcanoes' },
      { id: 'hosp', name: 'Hot Springs', state: 'AR', established: 1921, highlight: 'Historic bathhouses fed by geothermal mineral springs' },
      { id: 'indu', name: 'Indiana Dunes', state: 'IN', established: 2019, highlight: 'Lake Michigan shifting dunes & rich wetlands' },
      { id: 'isro', name: 'Isle Royale', state: 'MI', established: 1940, highlight: 'Isolated Lake Superior wilderness island of wolves and moose' },
      { id: 'jotr', name: 'Joshua Tree', state: 'CA', established: 1994, highlight: 'Twisted Joshua trees & giant granite boulders' },
      { id: 'katm', name: 'Katmai', state: 'AK', established: 1980, highlight: 'Brooks Falls brown bears catching sockeye salmon' },
      { id: 'kefj', name: 'Kenai Fjords', state: 'AK', established: 1980, highlight: 'Harding Icefield & glacier-carved marine fjords' },
      { id: 'kica', name: 'Kings Canyon', state: 'CA', established: 1940, highlight: 'Giant sequoia groves & deep granite valley' },
      { id: 'kova', name: 'Kobuk Valley', state: 'AK', established: 1980, highlight: 'Arctic sand dunes & Great Caribou migration routes' },
      { id: 'lacl', name: 'Lake Clark', state: 'AK', established: 1980, highlight: 'Steaming volcanoes, turquoise alpine waters & salmon runs' },
      { id: 'lavo', name: 'Lassen Volcanic', state: 'CA', established: 1916, highlight: 'Sulfur Works, bubbling mud pots & plug dome peak' },
      { id: 'maca', name: 'Mammoth Cave', state: 'KY', established: 1941, highlight: 'Longest known underground cave system in the world' },
      { id: 'meve', name: 'Mesa Verde', state: 'CO', established: 1906, highlight: 'Ancient Ancestral Puebloan cliff dwellings' },
      { id: 'mora', name: 'Mount Rainier', state: 'WA', established: 1899, highlight: '14,411 ft glaciated active stratovolcano & wildflower meadows' },
      { id: 'neri', name: 'New River Gorge', state: 'WV', established: 2020, highlight: 'Dramatic whitewater gorge and steel arch bridge' },
      { id: 'noca', name: 'North Cascades', state: 'WA', established: 1968, highlight: '300+ alpine glaciers & jagged rugged crests' },
      { id: 'olym', name: 'Olympic', state: 'WA', established: 1938, highlight: 'Hoh Rain Forest, Pacific coast & glaciated peaks' },
      { id: 'pefo', name: 'Petrified Forest', state: 'AZ', established: 1962, highlight: 'Rainbow crystallized wood logs & Painted Desert' },
      { id: 'pinn', name: 'Pinnacles', state: 'CA', established: 2013, highlight: 'Talus caves & soaring California condors' },
      { id: 'redw', name: 'Redwood', state: 'CA', established: 1968, highlight: 'Tallest living trees on Earth beside the Pacific' },
      { id: 'romo', name: 'Rocky Mountain', state: 'CO', established: 1915, highlight: 'Continental Divide Trail Ridge Road & subalpine lakes' },
      { id: 'sagu', name: 'Saguaro', state: 'AZ', established: 1994, highlight: 'Majestic giant saguaro cacti forests in Sonoran Desert' },
      { id: 'sequ', name: 'Sequoia', state: 'CA', established: 1890, highlight: 'General Sherman Tree, highest tree volume on earth' },
      { id: 'shen', name: 'Shenandoah', state: 'VA', established: 1935, highlight: 'Skyline Drive panoramic views along Blue Ridge crest' },
      { id: 'thro', name: 'Theodore Roosevelt', state: 'ND', established: 1978, highlight: 'Colorful badlands where TR ranched bison & wild horses' },
      { id: 'viis', name: 'Virgin Islands', state: 'VI', established: 1956, highlight: 'Pristine Caribbean coral reefs and Trunk Bay' },
      { id: 'voya', name: 'Voyageurs', state: 'MN', established: 1975, highlight: 'Interconnected maze of pristine boreal waterways' },
      { id: 'whsa', name: 'White Sands', state: 'NM', established: 2019, highlight: 'World’s largest gypsum dunefield shining brilliant white' },
      { id: 'wica', name: 'Wind Cave', state: 'SD', established: 1903, highlight: 'Complex boxwork calcite formations & prairie bison' },
      { id: 'wrel', name: 'Wrangell-St. Elias', state: 'AK', established: 1980, highlight: 'America’s largest national park at 13.2 million acres' },
      { id: 'yell', name: 'Yellowstone', state: 'WY', established: 1872, highlight: 'Old Faithful geyser, Grand Prismatic Spring & wildlife' },
      { id: 'yose', name: 'Yosemite', state: 'CA', established: 1890, highlight: 'El Capitan, Half Dome, roaring falls & sequoias' },
      { id: 'zion', name: 'Zion', state: 'UT', established: 1919, highlight: 'Angels Landing, The Narrows canyon & red sandstone' }
    ];

    function safeStorageGet(key, defaultVal) {
      try {
        const val = localStorage.getItem(key);
        if (val === null || val === undefined) return defaultVal;
        return JSON.parse(val);
      } catch (err) {
        return defaultVal;
      }
    }

    const defaultProfile = { id: 'p_primary', name: 'You', avatar: '🌲', color: '#10b981', isKid: false };

    let loadedProfiles = safeStorageGet('wl_v2_profiles', [defaultProfile]);
    if (!Array.isArray(loadedProfiles) || loadedProfiles.length === 0) {
      loadedProfiles = [defaultProfile];
    }

    const AppState = {
      isAuthenticated: localStorage.getItem('wl_v2_auth_status') === 'true',
      authMode: 'signin',
      authError: '',
      authForm: { name: '', email: '', password: '', confirmPassword: '', avatar: '🌲' },
      savedAccounts: safeStorageGet('wl_v2_registered_accounts', []),
      rememberedEmails: safeStorageGet('wl_v2_remembered_emails', []),
      rememberEmailChecked: true,
      
      profiles: loadedProfiles,
      activeProfileId: 'p_primary',
      activeTab: 'dashboard',
      passportSubTab: 'stamps',
      googleAuthIndex: localStorage.getItem('wl_v2_google_idx') || '0',

      records: safeStorageGet('wl_v2_records', {}),
      stamps: safeStorageGet('wl_v2_stamps', []),
      badges: safeStorageGet('wl_v2_badges', []),
      trips: safeStorageGet('wl_v2_trips', []),

      showEditNameModal: false,
      showAddProfileModal: false,
      showAddStampModal: false,
      showAddBadgeModal: false,
      showAddTripModal: false,
      showDeleteAccountModal: false,

      parkSearch: '',
      parkRegion: 'ALL',
      stateRegion: 'ALL'
    };

    if (AppState.rememberedEmails && AppState.rememberedEmails.length > 0) {
      AppState.authForm.email = AppState.rememberedEmails[0];
    }

    function saveState() {
      try {
        localStorage.setItem('wl_v2_auth_status', AppState.isAuthenticated ? 'true' : 'false');
        localStorage.setItem('wl_v2_registered_accounts', JSON.stringify(AppState.savedAccounts));
        localStorage.setItem('wl_v2_remembered_emails', JSON.stringify(AppState.rememberedEmails));
        localStorage.setItem('wl_v2_profiles', JSON.stringify(AppState.profiles));
        localStorage.setItem('wl_v2_records', JSON.stringify(AppState.records));
        localStorage.setItem('wl_v2_stamps', JSON.stringify(AppState.stamps));
        localStorage.setItem('wl_v2_badges', JSON.stringify(AppState.badges));
        localStorage.setItem('wl_v2_trips', JSON.stringify(AppState.trips));
        localStorage.setItem('wl_v2_google_idx', AppState.googleAuthIndex);
      } catch (err) {
        console.warn('Storage save failed:', err);
      }
    }

    function showToast(message, type = 'emerald') {
      const c = document.getElementById('toast-container');
      if (!c) return;
      const toast = document.createElement('div');
      toast.className = `bg-slate-900 border border-${type}-500/50 text-slate-100 text-xs px-4 py-2.5 rounded-xl shadow-2xl flex items-center gap-2 transform transition-all duration-300 translate-y-2 opacity-0`;
      toast.innerHTML = `<span class="w-2 h-2 rounded-full bg-${type}-400"></span> <span>${message}</span>`;
      c.appendChild(toast);
      setTimeout(() => { toast.classList.remove('translate-y-2', 'opacity-0'); }, 10);
      setTimeout(() => {
        toast.classList.add('opacity-0', 'translate-y-2');
        setTimeout(() => toast.remove(), 300);
      }, 3000);
    }

    function getActiveProfile() {
      if (AppState.activeProfileId === 'all') {
        return { id: 'all', name: 'All Household', avatar: '👨‍👩‍👧‍👦', color: '#10b981' };
      }
      const found = AppState.profiles.find(p => p && p.id === AppState.activeProfileId);
      if (found) return found;
      return AppState.profiles[0] || defaultProfile;
    }

    function renderAvatarHTML(avatar, className = "w-6 h-6") {
      if (typeof avatar === 'string' && (avatar.startsWith('data:image') || avatar.startsWith('http'))) {
        return `<img src="${avatar}" alt="Avatar" class="${className} object-cover rounded-full inline-block shrink-0 border border-slate-700" />`;
      }
      return `<span class="${className} inline-flex items-center justify-center shrink-0 text-base leading-none select-none">${avatar || '🌲'}</span>`;
    }

    function render() {
      const root = document.getElementById('app');
      if (!root) return;

      try {
        if (!AppState.isAuthenticated) {
          root.innerHTML = renderAuthView();
          bindAuthEvents();
        } else {
          root.innerHTML = renderMainLayout();
          bindMainEvents();
        }
      } catch (err) {
        console.error('Render error:', err);
        root.innerHTML = `
          <div class="min-h-screen flex items-center justify-center p-6 bg-slate-950 text-center">
            <div class="max-w-md w-full bg-slate-900 border border-slate-800 p-6 rounded-3xl space-y-4">
              <div class="text-3xl">🧭</div>
              <h2 class="text-lg font-bold text-white">WanderLog Ready</h2>
              <p class="text-xs text-slate-400">Click below to start fresh.</p>
              <button onclick="localStorage.clear(); location.reload();" class="px-4 py-2 bg-emerald-500 text-slate-950 font-bold rounded-xl text-xs">Reset to Clean View</button>
            </div>
          </div>
        `;
      }
    }

    function renderAuthView() {
      return `
        <div class="min-h-screen flex items-center justify-center p-4 bg-slate-950">
          <div class="max-w-md w-full bg-slate-900 border border-slate-800 rounded-3xl p-6 sm:p-8 shadow-2xl space-y-6">
            <div class="text-center space-y-2">
              <div class="w-12 h-12 rounded-2xl bg-emerald-500 flex items-center justify-center mx-auto text-slate-950 font-black shadow-lg shadow-emerald-500/20">
                🧭
              </div>
              <h1 class="text-2xl font-black bg-gradient-to-r from-emerald-400 to-teal-200 bg-clip-text text-transparent">
                ${AppState.authMode === 'signup' ? 'Welcome to WanderLog' : 'Sign In to WanderLog'}
              </h1>
              <p class="text-xs text-slate-400">Track 50 US States, 63 National Parks, Stamps & Badges.</p>
            </div>

            <div class="grid grid-cols-2 p-1 bg-slate-950 rounded-xl border border-slate-800 text-xs font-bold">
              <button id="toggle-signin-btn" class="py-2 rounded-lg transition-all ${AppState.authMode === 'signin' ? 'bg-emerald-500 text-slate-950 font-black shadow' : 'text-slate-400 hover:text-white'}">
                Sign In
              </button>
              <button id="toggle-signup-btn" class="py-2 rounded-lg transition-all ${AppState.authMode === 'signup' ? 'bg-emerald-500 text-slate-950 font-black shadow' : 'text-slate-400 hover:text-white'}">
                Sign Up
              </button>
            </div>

            ${AppState.authError ? `
              <div class="p-3 rounded-xl bg-rose-950/70 border border-rose-800 text-rose-300 text-xs">
                ${AppState.authError}
              </div>
            ` : ''}

            <form id="auth-form" class="space-y-4">
              ${AppState.authMode === 'signup' ? `
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Your Name</label>
                  <input type="text" id="auth-name" required placeholder="e.g. Alex" value="${AppState.authForm.name || ''}" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3.5 py-2.5 text-sm text-slate-100 placeholder-slate-600 focus:outline-none focus:border-emerald-500" />
                </div>
              ` : ''}

              <div>
                <div class="flex items-center justify-between mb-1">
                  <label class="block text-xs font-semibold text-slate-300">Email Address</label>
                  ${AppState.authMode === 'signin' && AppState.rememberedEmails.length > 0 ? `
                    <span class="text-[10px] text-emerald-400 font-mono">${AppState.rememberedEmails.length} remembered</span>
                  ` : ''}
                </div>
                <input type="email" id="auth-email" required list="remembered-emails-list" placeholder="example@email.com" value="${AppState.authForm.email || ''}" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3.5 py-2.5 text-sm text-slate-100 placeholder-slate-600 focus:outline-none focus:border-emerald-500" />
                <datalist id="remembered-emails-list">
                  ${(AppState.rememberedEmails || []).map(em => `<option value="${em}"></option>`).join('')}
                </datalist>

                ${AppState.authMode === 'signin' && (AppState.rememberedEmails || []).length > 0 ? `
                  <div class="mt-2.5 space-y-1.5">
                    <span class="text-[10px] text-slate-400">Remembered emails:</span>
                    <div class="flex flex-wrap gap-1.5">
                      ${AppState.rememberedEmails.map(rem => `
                        <div class="inline-flex items-center space-x-1.5 px-2.5 py-1 rounded-lg text-xs font-mono border ${((AppState.authForm.email || '').toLowerCase() === rem.toLowerCase()) ? 'bg-emerald-950 border-emerald-500 text-emerald-300 shadow' : 'bg-slate-950 border-slate-800 text-slate-400 hover:text-white'}">
                          <button type="button" class="quick-fill-email hover:underline" data-email="${rem}">${rem}</button>
                          <button type="button" class="forget-email-btn text-slate-600 hover:text-rose-400 ml-1" data-email="${rem}" title="Forget email">✕</button>
                        </div>
                      `).join('')}
                    </div>
                  </div>
                ` : ''}
              </div>

              <div>
                <label class="block text-xs font-semibold text-slate-300 mb-1">Password</label>
                <input type="password" id="auth-password" required placeholder="••••••••" value="${AppState.authForm.password || ''}" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3.5 py-2.5 text-sm text-slate-100 placeholder-slate-600 focus:outline-none focus:border-emerald-500" />
              </div>

              <div class="flex items-center space-x-2 pt-0.5">
                <input type="checkbox" id="remember-email-chk" ${AppState.rememberEmailChecked ? 'checked' : ''} class="rounded border-slate-800 bg-slate-950 text-emerald-500 focus:ring-0" />
                <label for="remember-email-chk" class="text-xs text-slate-400 cursor-pointer select-none">Remember this email address</label>
              </div>

              ${AppState.authMode === 'signup' ? `
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Confirm Password</label>
                  <input type="password" id="auth-confirm" required placeholder="••••••••" value="${AppState.authForm.confirmPassword || ''}" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3.5 py-2.5 text-sm text-slate-100 placeholder-slate-600 focus:outline-none focus:border-emerald-500" />
                </div>
              ` : ''}

              <button type="submit" class="w-full py-3 rounded-xl bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-extrabold text-sm transition-all shadow-lg shadow-emerald-500/20 mt-2">
                ${AppState.authMode === 'signup' ? 'Create Account & Begin' : 'Sign In'}
              </button>
            </form>
          </div>
        </div>
      `;
    }

    function renderMainLayout() {
      const active = getActiveProfile();
      let stVis = 0, pkVis = 0;
      US_STATES.forEach(s => { if (AppState.records[`${AppState.activeProfileId}_${s.id}`]?.visited) stVis++; });
      NATIONAL_PARKS.forEach(p => { if (AppState.records[`${AppState.activeProfileId}_${p.id}`]?.visited) pkVis++; });

      return `
        <header class="bg-slate-900 border-b border-slate-800 sticky top-0 z-30 px-4 sm:px-6 py-3 flex items-center justify-between">
          <div class="flex items-center space-x-3">
            <div class="w-9 h-9 rounded-xl bg-emerald-500 flex items-center justify-center text-slate-950 font-black shadow-md shadow-emerald-500/20">
              🧭
            </div>
            <div>
              <h1 class="font-black text-base tracking-tight text-white leading-none">WanderLog</h1>
              <span class="text-[10px] text-emerald-400 font-mono">Travel Passport & Maps Hub</span>
            </div>
          </div>

          <div class="flex items-center space-x-2 sm:space-x-3">
            <div class="flex items-center bg-slate-950 border border-slate-800 rounded-xl px-2.5 py-1">
              ${renderAvatarHTML(active.avatar, 'w-5 h-5')}
              <select id="traveler-select" class="bg-transparent text-xs font-semibold text-slate-200 outline-none ml-2 cursor-pointer">
                <option value="all" ${AppState.activeProfileId === 'all' ? 'selected' : ''} class="bg-slate-900 text-slate-200">👨‍👩‍👧‍👦 All Household</option>
                ${(AppState.profiles || []).map(p => `
                  <option value="${p.id}" ${AppState.activeProfileId === p.id ? 'selected' : ''} class="bg-slate-900 text-slate-200">
                    ${p.name || 'Traveler'} ${p.isKid ? '(Junior Ranger)' : ''}
                  </option>
                `).join('')}
              </select>
            </div>

            <button id="btn-edit-name" class="p-1.5 text-xs bg-slate-800 hover:bg-slate-700 text-slate-300 rounded-xl border border-slate-700" title="Edit Profile Details">
              ✏️
            </button>

            <button id="btn-signout" class="text-xs bg-slate-800 hover:bg-slate-700 text-slate-300 px-3 py-1.5 rounded-xl border border-slate-700 transition font-medium">
              Sign Out
            </button>
          </div>
        </header>

        <nav class="bg-slate-900/60 border-b border-slate-800 px-4 flex space-x-2 overflow-x-auto py-2.5">
          ${[
            { id: 'dashboard', label: 'Dashboard', icon: '📊' },
            { id: 'states', label: '50 States Map', icon: '🗺️' },
            { id: 'parks', label: '63 National Parks', icon: '🏞️' },
            { id: 'passport', label: 'Passport & Badges', icon: '🎖️' },
            { id: 'trips', label: 'Trip Planner', icon: '🎒' },
            { id: 'maps', label: 'Google Maps Hub', icon: '📍' },
            { id: 'settings', label: 'Settings', icon: '⚙️' }
          ].map(t => `
            <button class="nav-tab-btn flex items-center space-x-2 px-3.5 py-1.5 rounded-xl text-xs font-bold whitespace-nowrap transition-all ${AppState.activeTab === t.id ? 'bg-emerald-500 text-slate-950 shadow-md font-extrabold' : 'text-slate-400 hover:text-white bg-slate-950/40 border border-slate-800/80'}" data-tab="${t.id}">
              <span>${t.icon}</span>
              <span>${t.label}</span>
            </button>
          `).join('')}
        </nav>

        <main class="flex-1 max-w-7xl mx-auto w-full p-4 sm:p-6 space-y-6">
          ${renderTabContent(stVis, pkVis, active)}
        </main>

        ${renderActiveModals(active)}
      `;
    }

    function renderTabContent(stVis, pkVis, active) {
      if (AppState.activeTab === 'dashboard') {
        return `
          <div class="space-y-6">
            <div class="bg-gradient-to-r from-emerald-950/70 via-slate-900 to-slate-900 border border-emerald-800/40 rounded-3xl p-6 sm:p-8">
              <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4">
                <div>
                  <h2 class="text-xl sm:text-3xl font-black text-white">Travel Tracker & Expedition Hub</h2>
                  <p class="text-slate-400 text-xs sm:text-sm mt-1">Logged in for: <span class="text-emerald-400 font-bold">${active.name}</span></p>
                </div>
                <div class="flex items-center gap-2">
                  <button id="dash-quick-trip" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 text-xs font-bold px-4 py-2 rounded-xl transition shadow-lg shadow-emerald-500/20">
                    + Plan New Trip
                  </button>
                  <button id="dash-quick-stamp" class="bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-bold px-4 py-2 rounded-xl border border-slate-700 transition">
                    + Log Stamp
                  </button>
                </div>
              </div>

              <div class="grid grid-cols-2 sm:grid-cols-4 gap-3.5 mt-6">
                <div class="bg-slate-950/80 border border-slate-800 p-4 rounded-2xl text-center">
                  <span class="text-xs text-slate-400">States Visited</span>
                  <div class="text-3xl font-black text-emerald-400 font-mono mt-1">${stVis}/50</div>
                  <span class="text-[10px] text-slate-500 font-mono">${Math.round((stVis/50)*100)}% Complete</span>
                </div>

                <div class="bg-slate-950/80 border border-slate-800 p-4 rounded-2xl text-center">
                  <span class="text-xs text-slate-400">National Parks</span>
                  <div class="text-3xl font-black text-teal-400 font-mono mt-1">${pkVis}/63</div>
                  <span class="text-[10px] text-slate-500 font-mono">${Math.round((pkVis/63)*100)}% Complete</span>
                </div>

                <div class="bg-slate-950/80 border border-slate-800 p-4 rounded-2xl text-center">
                  <span class="text-xs text-slate-400">Passport Stamps</span>
                  <div class="text-3xl font-black text-amber-400 font-mono mt-1">${(AppState.stamps || []).length}</div>
                  <span class="text-[10px] text-slate-500">Collected</span>
                </div>

                <div class="bg-slate-950/80 border border-slate-800 p-4 rounded-2xl text-center">
                  <span class="text-xs text-slate-400">Junior Ranger Badges</span>
                  <div class="text-3xl font-black text-indigo-400 font-mono mt-1">${(AppState.badges || []).length}</div>
                  <span class="text-[10px] text-slate-500">Earned</span>
                </div>
              </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 space-y-3">
                <div class="flex items-center justify-between">
                  <h3 class="font-bold text-sm text-slate-100">Upcoming Expeditions</h3>
                  <button class="nav-tab-btn text-xs text-emerald-400 hover:underline" data-tab="trips">View All →</button>
                </div>
                ${(AppState.trips || []).length === 0 ? `
                  <p class="text-xs text-slate-500 py-4 text-center">No upcoming trips planned yet. Click '+ Plan New Trip' above to start!</p>
                ` : `
                  <div class="space-y-2">
                    ${AppState.trips.slice(0, 3).map(tr => `
                      <div class="bg-slate-950 border border-slate-800 p-3 rounded-xl flex items-center justify-between text-xs">
                        <div>
                          <div class="font-bold text-slate-200">${tr.title || 'Trip'}</div>
                          <div class="text-[11px] text-slate-400">${tr.destination || 'Unspecified'} • ${tr.startDate || 'TBD'}</div>
                        </div>
                        <span class="px-2 py-0.5 rounded bg-emerald-950 border border-emerald-800 text-emerald-400 text-[10px] font-mono">Ready</span>
                      </div>
                    `).join('')}
                  </div>
                `}
              </div>

              <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 space-y-3">
                <div class="flex items-center justify-between">
                  <h3 class="font-bold text-sm text-slate-100">Google Maps Multi-Account Routing</h3>
                  <button class="nav-tab-btn text-xs text-emerald-400 hover:underline" data-tab="maps">Maps Hub →</button>
                </div>
                <p class="text-xs text-slate-400">Currently routing directions with Google account parameter:</p>
                <div class="bg-slate-950 border border-slate-800 p-3 rounded-xl flex items-center justify-between text-xs font-mono">
                  <span class="text-slate-300">authuser=${AppState.googleAuthIndex}</span>
                  <a href="https://maps.google.com/?authuser=${AppState.googleAuthIndex}" target="_blank" class="text-emerald-400 hover:underline">Open Maps ↗</a>
                </div>
              </div>
            </div>
          </div>
        `;
      }

      if (AppState.activeTab === 'states') {
        return `
          <div class="space-y-4">
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 shadow-xl space-y-4">
              <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3">
                <div>
                  <h3 class="font-bold text-base text-slate-100">50 US States Cartogram Map</h3>
                  <p class="text-xs text-slate-400">Click any state tile to cycle: <span class="text-slate-400">Unvisited</span> → <span class="text-emerald-400 font-bold">Visited</span> → <span class="text-amber-400 font-bold">Wishlist</span>.</p>
                </div>
                <div class="flex items-center gap-3 text-xs">
                  <span class="flex items-center gap-1.5"><span class="w-3 h-3 rounded bg-emerald-500"></span> Visited</span>
                  <span class="flex items-center gap-1.5"><span class="w-3 h-3 rounded bg-amber-500"></span> Wishlist</span>
                  <span class="flex items-center gap-1.5"><span class="w-3 h-3 rounded bg-slate-800 border border-slate-700"></span> Unvisited</span>
                </div>
              </div>

              <div class="overflow-x-auto pb-2">
                <div class="min-w-[680px] grid grid-cols-12 gap-1.5 p-3 bg-slate-950 rounded-2xl border border-slate-800">
                  ${Array.from({ length: 8 }).map((_, r) => {
                    return Array.from({ length: 12 }).map((_, c) => {
                      const cell = MAP_GRID.find(item => item.r === r && item.c === c);
                      if (!cell) return `<div class="h-11 invisible"></div>`;
                      const st = US_STATES.find(s => s.id === cell.id);
                      const recKey = `${AppState.activeProfileId}_${cell.id}`;
                      const rec = AppState.records[recKey] || {};

                      let bgClass = 'bg-slate-900 border-slate-800 text-slate-400 hover:border-slate-700';
                      if (rec.visited) bgClass = 'bg-emerald-950 border-emerald-500 text-emerald-300 font-black shadow-lg shadow-emerald-950';
                      else if (rec.wishlist) bgClass = 'bg-amber-950 border-amber-500 text-amber-300 font-black shadow-lg shadow-amber-950';

                      return `
                        <button class="state-cell-btn h-11 rounded-xl flex flex-col items-center justify-center border text-xs font-mono font-bold transition-all ${bgClass}" data-state="${cell.id}" title="${st ? st.name : cell.id}">
                          <span>${cell.id}</span>
                          ${rec.visited ? '<span class="text-[9px] leading-none">✓</span>' : rec.wishlist ? '<span class="text-[9px] leading-none">★</span>' : ''}
                        </button>
                      `;
                    }).join('');
                  }).join('')}
                </div>
              </div>
            </div>

            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 space-y-4">
              <div class="flex items-center justify-between">
                <h4 class="font-bold text-sm text-slate-100">All 50 US States Directory</h4>
                <select id="state-region-filter" class="bg-slate-950 border border-slate-800 rounded-xl px-3 py-1.5 text-xs text-slate-200">
                  <option value="ALL">All Regions</option>
                  <option value="West">West</option>
                  <option value="Midwest">Midwest</option>
                  <option value="South">South</option>
                  <option value="Northeast">Northeast</option>
                </select>
              </div>

              <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2">
                ${US_STATES.filter(s => AppState.stateRegion === 'ALL' || s.region === AppState.stateRegion).map(s => {
                  const rec = AppState.records[`${AppState.activeProfileId}_${s.id}`] || {};
                  return `
                    <div class="bg-slate-950 border border-slate-800 p-2.5 rounded-xl flex items-center justify-between text-xs">
                      <div>
                        <div class="font-bold text-slate-200">${s.name}</div>
                        <div class="text-[10px] text-slate-500">${s.id} • ${s.region}</div>
                      </div>
                      <div class="flex items-center gap-1">
                        <button class="toggle-state-vis px-2 py-1 rounded text-[10px] font-bold ${rec.visited ? 'bg-emerald-600 text-white' : 'bg-slate-800 text-slate-400 hover:text-white'}" data-state="${s.id}">
                          ${rec.visited ? '✓' : 'Visit'}
                        </button>
                      </div>
                    </div>
                  `;
                }).join('')}
              </div>
            </div>
          </div>
        `;
      }

      if (AppState.activeTab === 'parks') {
        const filteredParks = NATIONAL_PARKS.filter(p => {
          return p.name.toLowerCase().includes(AppState.parkSearch.toLowerCase()) || p.state.toLowerCase().includes(AppState.parkSearch.toLowerCase());
        });

        return `
          <div class="space-y-4">
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 flex flex-col sm:flex-row items-center justify-between gap-3">
              <input type="text" id="park-search-input" placeholder="Search 63 National Parks by name or state..." value="${AppState.parkSearch}" class="w-full sm:w-80 bg-slate-950 border border-slate-800 rounded-xl px-3.5 py-2 text-xs text-slate-100 focus:outline-none focus:border-emerald-500" />
              <div class="text-xs text-slate-400 font-mono">Showing ${filteredParks.length} of 63 National Parks</div>
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-3.5">
              ${filteredParks.map(p => {
                const recKey = `${AppState.activeProfileId}_${p.id}`;
                const rec = AppState.records[recKey] || {};
                const mapsUrl = `https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(p.name + ' National Park')}&authuser=${AppState.googleAuthIndex}`;

                return `
                  <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 flex flex-col justify-between space-y-3 hover:border-slate-700 transition">
                    <div>
                      <div class="flex items-start justify-between">
                        <div>
                          <h4 class="font-bold text-sm text-slate-100">${p.name}</h4>
                          <span class="text-[11px] font-mono text-emerald-400">${p.state} • Est. ${p.established}</span>
                        </div>
                        <span class="text-xl">🏞️</span>
                      </div>
                      <p class="text-xs text-slate-400 mt-2 leading-relaxed">${p.highlight}</p>
                    </div>

                    <div class="pt-3 border-t border-slate-800 flex items-center justify-between">
                      <button class="park-visit-toggle px-3 py-1.5 rounded-xl text-xs font-bold transition flex items-center gap-1.5 ${rec.visited ? 'bg-emerald-600 text-white shadow-md' : 'bg-slate-800 text-slate-300 hover:text-white'}" data-park="${p.id}">
                        <span>${rec.visited ? '✓ Visited' : 'Mark Visited'}</span>
                      </button>

                      <a href="${mapsUrl}" target="_blank" rel="noopener noreferrer" class="text-xs text-sky-400 hover:text-sky-300 flex items-center gap-1 bg-sky-950/60 border border-sky-800 px-2.5 py-1.5 rounded-xl">
                        <span>Maps ↗</span>
                      </a>
                    </div>
                  </div>
                `;
              }).join('')}
            </div>
          </div>
        `;
      }

      if (AppState.activeTab === 'passport') {
        const filteredStamps = AppState.activeProfileId === 'all'
          ? (AppState.stamps || [])
          : (AppState.stamps || []).filter(s => s.profileId === AppState.activeProfileId);

        const filteredBadges = AppState.activeProfileId === 'all'
          ? (AppState.badges || [])
          : (AppState.badges || []).filter(b => b.profileId === AppState.activeProfileId);

        return `
          <div class="space-y-4">
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4">
              <div>
                <h3 class="font-bold text-base text-slate-100">Passport Stamps & Junior Ranger Showcase</h3>
                <p class="text-xs text-slate-400">Official NPS ink cancellations and authentic Junior Ranger badges.</p>
              </div>

              <div class="flex items-center gap-2">
                <button id="open-stamp-modal" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 text-xs font-extrabold px-3.5 py-2 rounded-xl transition shadow">
                  + Add Cancellation Stamp
                </button>
                <button id="open-badge-modal" class="bg-amber-500 hover:bg-amber-400 text-slate-950 text-xs font-extrabold px-3.5 py-2 rounded-xl transition shadow">
                  + Add Junior Ranger Badge
                </button>
              </div>
            </div>

            <div class="flex space-x-2 border-b border-slate-800 pb-2">
              <button class="passport-subtab-btn px-4 py-1.5 rounded-xl text-xs font-bold ${AppState.passportSubTab === 'stamps' ? 'bg-emerald-500 text-slate-950 font-black' : 'text-slate-400 hover:text-white bg-slate-900'}" data-sub="stamps">
                📮 Ink Cancellation Stamps (${filteredStamps.length})
              </button>
              <button class="passport-subtab-btn px-4 py-1.5 rounded-xl text-xs font-bold ${AppState.passportSubTab === 'badges' ? 'bg-amber-500 text-slate-950 font-black' : 'text-slate-400 hover:text-white bg-slate-900'}" data-sub="badges">
                ⭐ Junior Ranger Badges (${filteredBadges.length})
              </button>
            </div>

            ${AppState.passportSubTab === 'stamps' ? `
              ${filteredStamps.length === 0 ? `
                <div class="bg-slate-900/50 border border-dashed border-slate-800 rounded-3xl p-10 text-center space-y-3">
                  <div class="text-4xl">📮</div>
                  <h4 class="font-bold text-sm text-slate-200">No Cancellation Stamps Logged Yet</h4>
                  <p class="text-xs text-slate-400 max-w-md mx-auto">Get your National Parks passport stamped at any visitor center, and log the official ink cancellation details here.</p>
                  <button id="empty-stamp-btn" class="bg-emerald-500 text-slate-950 text-xs font-bold px-4 py-2 rounded-xl">Add Your First Stamp</button>
                </div>
              ` : `
                <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
                  ${filteredStamps.map(st => `
                    <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 flex flex-col items-center text-center space-y-3 relative overflow-hidden">
                      <div class="w-32 h-32 rounded-full border-4 border-dashed p-1.5 flex flex-col items-center justify-center font-mono select-none" style="color: ${st.inkColor || '#10b981'}; border-color: ${st.inkColor || '#10b981'};">
                        <span class="text-[9px] font-black uppercase tracking-widest">NATIONAL PARK SERVICE</span>
                        <span class="text-xs font-extrabold my-1 text-center leading-tight line-clamp-2">${st.parkName}</span>
                        <span class="text-[10px] font-bold">${st.date || 'PASSPORT'}</span>
                        <span class="text-[8px] uppercase">${st.location || 'VISITOR CENTER'}</span>
                      </div>

                      <div class="w-full text-left pt-2 border-t border-slate-800 text-xs space-y-1">
                        <div class="font-bold text-slate-200 truncate">${st.parkName}</div>
                        <div class="text-[11px] text-slate-400">${st.location || 'Visitor Center'} • ${st.state}</div>
                        ${st.notes ? `<p class="text-[11px] text-slate-500 italic mt-1">${st.notes}</p>` : ''}
                      </div>

                      <button class="delete-stamp-btn text-[10px] text-slate-500 hover:text-rose-400 self-end" data-id="${st.id}">Remove</button>
                    </div>
                  `).join('')}
                </div>
              `}
            ` : `
              ${filteredBadges.length === 0 ? `
                <div class="bg-slate-900/50 border border-dashed border-slate-800 rounded-3xl p-10 text-center space-y-3">
                  <div class="text-4xl">⭐</div>
                  <h4 class="font-bold text-sm text-slate-200">No Junior Ranger Badges Earned Yet</h4>
                  <p class="text-xs text-slate-400 max-w-md mx-auto">Complete the official booklet at any National Park or Monument to take the Junior Ranger oath and earn an authentic shield or badge.</p>
                  <button id="empty-badge-btn" class="bg-amber-500 text-slate-950 text-xs font-bold px-4 py-2 rounded-xl">Add Junior Ranger Badge</button>
                </div>
              ` : `
                <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
                  ${filteredBadges.map(bg => `
                    <div class="bg-slate-900 border border-amber-900/40 rounded-2xl p-5 flex flex-col items-center text-center space-y-3 relative">
                      <div class="w-16 h-16 rounded-2xl bg-amber-500/20 border border-amber-500/40 flex items-center justify-center text-3xl shadow-lg shadow-amber-500/10">
                        🛡️
                      </div>
                      <div>
                        <div class="font-extrabold text-sm text-amber-300">${bg.parkName}</div>
                        <span class="text-[10px] uppercase font-mono px-2 py-0.5 rounded bg-amber-950 border border-amber-700/60 text-amber-200">${bg.badgeType || 'Wooden Shield'}</span>
                      </div>
                      <div class="w-full text-left pt-2 border-t border-slate-800 text-xs space-y-1">
                        <div class="text-slate-400 text-[11px]">Sworn In: <span class="text-slate-200 font-bold">${bg.date || 'Official Oath'}</span></div>
                        ${bg.rangerName ? `<div class="text-slate-400 text-[11px]">Ranger: <span class="text-slate-200">${bg.rangerName}</span></div>` : ''}
                        ${bg.bookletDone ? `<div class="text-emerald-400 text-[11px]">✓ Activity Booklet Completed</div>` : ''}
                      </div>
                      <button class="delete-badge-btn text-[10px] text-slate-500 hover:text-rose-400 self-end" data-id="${bg.id}">Remove</button>
                    </div>
                  `).join('')}
                </div>
              `}
            `}
          </div>
        `;
      }

      if (AppState.activeTab === 'trips') {
        return `
          <div class="space-y-4">
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 flex items-center justify-between">
              <div>
                <h3 class="font-bold text-base text-slate-100">Expedition & Roadtrip Planner</h3>
                <p class="text-xs text-slate-400">Plan itineraries, packing checklists, and destinations.</p>
              </div>
              <button id="open-trip-modal" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 text-xs font-extrabold px-3.5 py-2 rounded-xl transition shadow">
                + Plan New Trip
              </button>
            </div>

            ${(AppState.trips || []).length === 0 ? `
              <div class="bg-slate-900/50 border border-dashed border-slate-800 rounded-3xl p-10 text-center space-y-3">
                <div class="text-4xl">🎒</div>
                <h4 class="font-bold text-sm text-slate-200">No Expeditions Planned Yet</h4>
                <p class="text-xs text-slate-400 max-w-md mx-auto">Create a roadtrip, organize dates, and map target National Parks and States.</p>
                <button id="empty-trip-btn" class="bg-emerald-500 text-slate-950 text-xs font-bold px-4 py-2 rounded-xl">Plan Your First Trip</button>
              </div>
            ` : `
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                ${AppState.trips.map(tr => `
                  <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 space-y-4 flex flex-col justify-between">
                    <div>
                      <div class="flex items-start justify-between">
                        <div>
                          <h4 class="font-bold text-base text-slate-100">${tr.title}</h4>
                          <span class="text-xs font-mono text-emerald-400">${tr.destination || 'US Tour'}</span>
                        </div>
                        <span class="px-2.5 py-1 rounded-xl bg-slate-950 border border-slate-800 text-[11px] font-mono text-slate-400">${tr.startDate || 'Upcoming'}</span>
                      </div>
                      ${tr.notes ? `<p class="text-xs text-slate-400 mt-2 leading-relaxed">${tr.notes}</p>` : ''}
                    </div>

                    <div class="pt-3 border-t border-slate-800 flex items-center justify-between">
                      <a href="https://www.google.com/maps/dir/?api=1&destination=${encodeURIComponent(tr.destination)}&authuser=${AppState.googleAuthIndex}" target="_blank" class="text-xs text-sky-400 hover:underline flex items-center gap-1">
                        Open in Google Maps ↗
                      </a>
                      <button class="delete-trip-btn text-xs text-rose-400 hover:text-rose-300" data-id="${tr.id}">Delete</button>
                    </div>
                  </div>
                `).join('')}
              </div>
            `}
          </div>
        `;
      }

      if (AppState.activeTab === 'maps') {
        return `
          <div class="max-w-2xl mx-auto space-y-6">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-xl space-y-5">
              <div class="flex items-center space-x-3">
                <div class="w-12 h-12 rounded-2xl bg-rose-500/20 text-rose-400 flex items-center justify-center text-2xl font-black">
                  📍
                </div>
                <div>
                  <h3 class="font-extrabold text-base text-slate-100">Google Maps Account Switcher</h3>
                  <p class="text-xs text-slate-400">Routes every park link directly into your preferred Google account profile.</p>
                </div>
              </div>

              <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800 space-y-3">
                <label class="block text-xs font-bold text-slate-300">Choose Active Google Account:</label>
                <select id="google-auth-select" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-xs font-mono text-slate-200 focus:outline-none focus:border-emerald-500">
                  <option value="0" ${AppState.googleAuthIndex === '0' ? 'selected' : ''}>Default Google Account (authuser=0)</option>
                  <option value="1" ${AppState.googleAuthIndex === '1' ? 'selected' : ''}>Secondary Google Account (authuser=1)</option>
                  <option value="2" ${AppState.googleAuthIndex === '2' ? 'selected' : ''}>Third Google Account (authuser=2)</option>
                  <option value="3" ${AppState.googleAuthIndex === '3' ? 'selected' : ''}>Fourth Google Account (authuser=3)</option>
                </select>
                <p class="text-[11px] text-slate-500">When multiple accounts are logged into Chrome/Google, this parameter ensures links open in the exact designated profile.</p>
              </div>

              <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800 space-y-3">
                <label class="block text-xs font-bold text-slate-300">Quick-Launch Directions to Any National Park:</label>
                <div class="flex gap-2">
                  <select id="quick-map-park" class="flex-1 bg-slate-900 border border-slate-700 rounded-xl px-3 py-2 text-xs text-slate-200">
                    ${NATIONAL_PARKS.map(p => `<option value="${p.name} National Park">${p.name} (${p.state})</option>`).join('')}
                  </select>
                  <button id="btn-launch-map" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 text-xs font-bold px-4 py-2 rounded-xl transition">
                    Launch ↗
                  </button>
                </div>
              </div>
            </div>
          </div>
        `;
      }

      if (AppState.activeTab === 'settings') {
        return `
          <div class="max-w-2xl mx-auto space-y-6">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-xl space-y-4">
              <div class="flex items-center justify-between">
                <div>
                  <h3 class="font-extrabold text-base text-slate-100">Travelers in this Account</h3>
                  <p class="text-xs text-slate-400">Add kids, partners, or friends to keep individual passports.</p>
                </div>
                <button id="btn-add-profile" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 text-xs font-bold px-3 py-1.5 rounded-xl transition">
                  + Add Traveler
                </button>
              </div>

              <div class="space-y-2">
                ${(AppState.profiles || []).map(p => `
                  <div class="bg-slate-950 border border-slate-800 p-3.5 rounded-2xl flex items-center justify-between">
                    <div class="flex items-center space-x-3">
                      ${renderAvatarHTML(p.avatar, 'w-8 h-8')}
                      <div>
                        <div class="font-bold text-sm text-slate-200">${p.name}</div>
                        <div class="text-[10px] text-slate-500">${p.isKid ? 'Junior Ranger (Kid Profile)' : 'Explorer'}</div>
                      </div>
                    </div>
                    <div class="flex items-center gap-2">
                      ${p.id !== 'p_primary' ? `
                        <button class="delete-profile-btn text-xs text-slate-500 hover:text-rose-400" data-id="${p.id}">Remove</button>
                      ` : '<span class="text-[10px] text-slate-500 font-mono">Primary</span>'}
                    </div>
                  </div>
                `).join('')}
              </div>
            </div>

            <div class="bg-rose-950/20 border border-rose-900/40 rounded-3xl p-6 shadow-xl space-y-3">
              <h3 class="font-extrabold text-base text-rose-300">Danger Zone: Delete Account</h3>
              <p class="text-xs text-slate-400">Permanently clears your login, all travelers, passport stamps, junior ranger badges, and trip itineraries.</p>
              <button id="btn-open-delete-account" class="bg-rose-600 hover:bg-rose-500 text-white text-xs font-bold px-4 py-2 rounded-xl transition">
                Delete Entire Account & Data
              </button>
            </div>
          </div>
        `;
      }

      return '';
    }

    function renderActiveModals(active) {
      let modalsHTML = '';

      if (AppState.showEditNameModal) {
        modalsHTML += `
          <div class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 flex items-center justify-center p-4">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 max-w-sm w-full space-y-4 shadow-2xl">
              <h3 class="font-bold text-base text-slate-100">Edit Traveler Profile</h3>
              <form id="edit-name-form" class="space-y-3">
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Traveler Name</label>
                  <input type="text" id="edit-name-input" required value="${active.name}" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                </div>
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Pick Avatar Emoji</label>
                  <div class="flex flex-wrap gap-2 text-xl">
                    ${['🌲', '🧒', '🎒', '🐻', '🦅', '🛶', '⛺', '🧭', '🏕️', '🧗'].map(emo => `
                      <button type="button" class="pick-avatar-btn p-2 rounded-xl bg-slate-950 border border-slate-800 hover:border-emerald-500" data-avatar="${emo}">${emo}</button>
                    `).join('')}
                  </div>
                </div>
                <div class="flex justify-end gap-2 pt-2">
                  <button type="button" id="close-edit-name" class="px-3 py-1.5 rounded-xl bg-slate-800 text-xs text-slate-300">Cancel</button>
                  <button type="submit" class="px-4 py-1.5 rounded-xl bg-emerald-500 text-slate-950 font-bold text-xs">Save Changes</button>
                </div>
              </form>
            </div>
          </div>
        `;
      }

      if (AppState.showAddProfileModal) {
        modalsHTML += `
          <div class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 flex items-center justify-center p-4">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 max-w-sm w-full space-y-4 shadow-2xl">
              <h3 class="font-bold text-base text-slate-100">Add New Traveler</h3>
              <form id="add-profile-form" class="space-y-3">
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Name</label>
                  <input type="text" id="new-profile-name" required placeholder="e.g. Maya" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                </div>
                <div class="flex items-center gap-2 pt-1">
                  <input type="checkbox" id="new-profile-iskid" class="rounded bg-slate-950 border-slate-800 text-emerald-500" />
                  <label for="new-profile-iskid" class="text-xs text-slate-400">Junior Ranger (Child Profile)</label>
                </div>
                <div class="flex justify-end gap-2 pt-2">
                  <button type="button" id="close-add-profile" class="px-3 py-1.5 rounded-xl bg-slate-800 text-xs text-slate-300">Cancel</button>
                  <button type="submit" class="px-4 py-1.5 rounded-xl bg-emerald-500 text-slate-950 font-bold text-xs">Add Profile</button>
                </div>
              </form>
            </div>
          </div>
        `;
      }

      if (AppState.showAddStampModal) {
        modalsHTML += `
          <div class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 flex items-center justify-center p-4">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 max-w-md w-full space-y-4 shadow-2xl">
              <h3 class="font-bold text-base text-slate-100">Record Cancellation Stamp</h3>
              <form id="add-stamp-form" class="space-y-3">
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">National Park / Site</label>
                  <select id="stamp-park-select" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100">
                    ${NATIONAL_PARKS.map(p => `<option value="${p.name}|${p.state}">${p.name} National Park (${p.state})</option>`).join('')}
                  </select>
                </div>
                <div class="grid grid-cols-2 gap-2">
                  <div>
                    <label class="block text-xs font-semibold text-slate-300 mb-1">Date</label>
                    <input type="date" id="stamp-date" required value="${new Date().toISOString().split('T')[0]}" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                  </div>
                  <div>
                    <label class="block text-xs font-semibold text-slate-300 mb-1">Ink Color</label>
                    <select id="stamp-color" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100 font-mono">
                      <option value="#10b981">Forest Green</option>
                      <option value="#38bdf8">Historic Blue</option>
                      <option value="#f59e0b">NPS Brown / Amber</option>
                      <option value="#f43f5e">Canyon Red</option>
                      <option value="#94a3b8">Classic Black</option>
                    </select>
                  </div>
                </div>
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Location / Visitor Center</label>
                  <input type="text" id="stamp-loc" placeholder="e.g. Visitor Center / South Rim" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                </div>
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Notes</label>
                  <input type="text" id="stamp-notes" placeholder="e.g. Great morning hike on Bright Angel Trail" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                </div>
                <div class="flex justify-end gap-2 pt-2">
                  <button type="button" id="close-stamp-modal" class="px-3 py-1.5 rounded-xl bg-slate-800 text-xs text-slate-300">Cancel</button>
                  <button type="submit" class="px-4 py-1.5 rounded-xl bg-emerald-500 text-slate-950 font-bold text-xs">Save Stamp</button>
                </div>
              </form>
            </div>
          </div>
        `;
      }

      if (AppState.showAddBadgeModal) {
        modalsHTML += `
          <div class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 flex items-center justify-center p-4">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 max-w-md w-full space-y-4 shadow-2xl">
              <h3 class="font-bold text-base text-slate-100">Add Junior Ranger Badge</h3>
              <form id="add-badge-form" class="space-y-3">
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">National Park / Site</label>
                  <select id="badge-park-select" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100">
                    ${NATIONAL_PARKS.map(p => `<option value="${p.name}|${p.state}">${p.name} National Park (${p.state})</option>`).join('')}
                  </select>
                </div>
                <div class="grid grid-cols-2 gap-2">
                  <div>
                    <label class="block text-xs font-semibold text-slate-300 mb-1">Date Sworn In</label>
                    <input type="date" id="badge-date" required value="${new Date().toISOString().split('T')[0]}" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                  </div>
                  <div>
                    <label class="block text-xs font-semibold text-slate-300 mb-1">Badge Type</label>
                    <select id="badge-type" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100">
                      <option value="Wooden Shield">Wooden Shield</option>
                      <option value="Bronze/Gold Badge">Bronze/Gold Badge</option>
                      <option value="Plastic Pin">Plastic Pin</option>
                      <option value="Embroidered Patch">Embroidered Patch</option>
                    </select>
                  </div>
                </div>
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Sworn in by Ranger</label>
                  <input type="text" id="badge-ranger" placeholder="e.g. Ranger Sarah" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                </div>
                <div class="flex items-center gap-2 pt-1">
                  <input type="checkbox" id="badge-booklet" checked class="rounded bg-slate-950 border-slate-800 text-amber-500" />
                  <label for="badge-booklet" class="text-xs text-slate-300">Activity booklet fully verified</label>
                </div>
                <div class="flex justify-end gap-2 pt-2">
                  <button type="button" id="close-badge-modal" class="px-3 py-1.5 rounded-xl bg-slate-800 text-xs text-slate-300">Cancel</button>
                  <button type="submit" class="px-4 py-1.5 rounded-xl bg-amber-500 text-slate-950 font-bold text-xs">Save Badge</button>
                </div>
              </form>
            </div>
          </div>
        `;
      }

      if (AppState.showAddTripModal) {
        modalsHTML += `
          <div class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 flex items-center justify-center p-4">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 max-w-md w-full space-y-4 shadow-2xl">
              <h3 class="font-bold text-base text-slate-100">Plan Expedition Trip</h3>
              <form id="add-trip-form" class="space-y-3">
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Trip Name</label>
                  <input type="text" id="trip-title" required placeholder="e.g. Utah Mighty 5 Roadtrip" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                </div>
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Primary Destination</label>
                  <select id="trip-dest" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100">
                    <optgroup label="All 63 US National Parks">
                      ${NATIONAL_PARKS.map(p => `<option value="${p.name} National Park">${p.name} National Park</option>`).join('')}
                    </optgroup>
                    <optgroup label="US States">
                      ${US_STATES.map(s => `<option value="${s.name}">${s.name}</option>`).join('')}
                    </optgroup>
                  </select>
                </div>
                <div class="grid grid-cols-2 gap-2">
                  <div>
                    <label class="block text-xs font-semibold text-slate-300 mb-1">Start Date</label>
                    <input type="date" id="trip-start" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                  </div>
                  <div>
                    <label class="block text-xs font-semibold text-slate-300 mb-1">End Date</label>
                    <input type="date" id="trip-end" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100" />
                  </div>
                </div>
                <div>
                  <label class="block text-xs font-semibold text-slate-300 mb-1">Notes & Packing List</label>
                  <textarea id="trip-notes" rows="2" placeholder="e.g. Bring sturdy boots, water filtration, bear spray..." class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100"></textarea>
                </div>
                <div class="flex justify-end gap-2 pt-2">
                  <button type="button" id="close-trip-modal" class="px-3 py-1.5 rounded-xl bg-slate-800 text-xs text-slate-300">Cancel</button>
                  <button type="submit" class="px-4 py-1.5 rounded-xl bg-emerald-500 text-slate-950 font-bold text-xs">Create Trip</button>
                </div>
              </form>
            </div>
          </div>
        `;
      }

      if (AppState.showDeleteAccountModal) {
        modalsHTML += `
          <div class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 flex items-center justify-center p-4">
            <div class="bg-slate-900 border border-rose-900/60 rounded-3xl p-6 max-w-sm w-full space-y-4 shadow-2xl">
              <h3 class="font-extrabold text-base text-rose-400">Confirm Account Deletion</h3>
              <p class="text-xs text-slate-300 leading-relaxed">
                This will permanently delete your account, all profiles, records, stamps, and trips. To confirm, please type <span class="font-bold text-white font-mono">DELETE</span> below:
              </p>
              <input type="text" id="delete-confirm-input" placeholder="Type DELETE" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-100 font-mono" />
              <div class="flex justify-end gap-2 pt-2">
                <button type="button" id="close-delete-modal" class="px-3 py-1.5 rounded-xl bg-slate-800 text-xs text-slate-300">Cancel</button>
                <button type="button" id="confirm-delete-btn" disabled class="px-4 py-1.5 rounded-xl bg-rose-600 opacity-50 text-white font-bold text-xs cursor-not-allowed">Delete Account</button>
              </div>
            </div>
          </div>
        `;
      }

      return modalsHTML;
    }

    function bindAuthEvents() {
      document.getElementById('toggle-signin-btn')?.addEventListener('click', () => {
        AppState.authMode = 'signin';
        AppState.authError = '';
        render();
      });

      document.getElementById('toggle-signup-btn')?.addEventListener('click', () => {
        AppState.authMode = 'signup';
        AppState.authError = '';
        render();
      });

      document.getElementById('remember-email-chk')?.addEventListener('change', (e) => {
        AppState.rememberEmailChecked = e.target.checked;
      });

      document.querySelectorAll('.quick-fill-email').forEach(btn => {
        btn.addEventListener('click', () => {
          AppState.authForm.email = btn.dataset.email;
          const input = document.getElementById('auth-email');
          if (input) input.value = btn.dataset.email;
        });
      });

      document.querySelectorAll('.forget-email-btn').forEach(btn => {
        btn.addEventListener('click', (e) => {
          e.stopPropagation();
          const em = btn.dataset.email;
          AppState.rememberedEmails = (AppState.rememberedEmails || []).filter(i => i !== em);
          saveState();
          render();
        });
      });

      document.getElementById('auth-form')?.addEventListener('submit', (e) => {
        e.preventDefault();
        AppState.authError = '';
        const email = (document.getElementById('auth-email')?.value || '').trim().toLowerCase();
        const pwd = document.getElementById('auth-password')?.value || '';

        if (AppState.authMode === 'signup') {
          const name = (document.getElementById('auth-name')?.value || '').trim();
          const conf = document.getElementById('auth-confirm')?.value || '';

          if (!name) { AppState.authError = 'Please enter your name.'; render(); return; }
          if (pwd.length < 4) { AppState.authError = 'Password must be at least 4 characters.'; render(); return; }
          if (pwd !== conf) { AppState.authError = 'Passwords do not match.'; render(); return; }
          if ((AppState.savedAccounts || []).some(a => a.email === email)) {
            AppState.authError = 'An account with this email already exists. Please Sign In.';
            render();
            return;
          }

          const newAcc = { id: 'acc_' + Date.now(), name, email, password: pwd, avatar: '🌲' };
          AppState.savedAccounts.push(newAcc);
          if (AppState.rememberEmailChecked) {
            AppState.rememberedEmails = Array.from(new Set([email, ...(AppState.rememberedEmails || [])]));
          }
          AppState.profiles = [{ id: 'p_primary', name, avatar: '🌲', color: '#10b981', isKid: false }];
          AppState.activeProfileId = 'p_primary';
          AppState.isAuthenticated = true;
          saveState();
          render();
        } else {
          const acc = (AppState.savedAccounts || []).find(a => a.email === email);
          if (!acc) {
            AppState.authError = 'No account found with this email. Please check your email or Sign Up.';
            render();
            return;
          }
          if (acc.password && acc.password !== pwd) {
            AppState.authError = 'Incorrect password. Please try again.';
            render();
            return;
          }

          if (AppState.rememberEmailChecked) {
            AppState.rememberedEmails = Array.from(new Set([email, ...(AppState.rememberedEmails || [])]));
          }

          if (!AppState.profiles || AppState.profiles.length === 0) {
            AppState.profiles = [{ id: 'p_primary', name: acc.name, avatar: acc.avatar || '🌲', color: '#10b981', isKid: false }];
          } else {
            AppState.profiles[0].name = acc.name;
          }
          AppState.activeProfileId = AppState.profiles[0].id;
          AppState.isAuthenticated = true;
          saveState();
          render();
        }
      });
    }

    function bindMainEvents() {
      document.getElementById('btn-signout')?.addEventListener('click', () => {
        AppState.isAuthenticated = false;
        AppState.authMode = 'signin';
        if (AppState.rememberedEmails && AppState.rememberedEmails.length > 0) {
          AppState.authForm.email = AppState.rememberedEmails[0];
        }
        saveState();
        render();
      });

      document.getElementById('traveler-select')?.addEventListener('change', (e) => {
        AppState.activeProfileId = e.target.value;
        render();
      });

      document.getElementById('btn-edit-name')?.addEventListener('click', () => {
        AppState.showEditNameModal = true;
        render();
      });

      document.querySelectorAll('.nav-tab-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          AppState.activeTab = btn.dataset.tab;
          render();
        });
      });

      document.getElementById('dash-quick-trip')?.addEventListener('click', () => {
        AppState.showAddTripModal = true;
        render();
      });
      document.getElementById('dash-quick-stamp')?.addEventListener('click', () => {
        AppState.showAddStampModal = true;
        render();
      });

      document.querySelectorAll('.state-cell-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          const stId = btn.dataset.state;
          const k = `${AppState.activeProfileId}_${stId}`;
          const current = AppState.records[k] || {};
          if (!current.visited && !current.wishlist) {
            AppState.records[k] = { visited: true, wishlist: false };
            showToast(`${stId} marked visited!`);
          } else if (current.visited) {
            AppState.records[k] = { visited: false, wishlist: true };
            showToast(`${stId} marked as wishlist!`, 'amber');
          } else {
            delete AppState.records[k];
            showToast(`${stId} unmarked.`);
          }
          saveState();
          render();
        });
      });

      document.querySelectorAll('.toggle-state-vis').forEach(btn => {
        btn.addEventListener('click', () => {
          const stId = btn.dataset.state;
          const k = `${AppState.activeProfileId}_${stId}`;
          const current = AppState.records[k] || {};
          AppState.records[k] = { visited: !current.visited, wishlist: false };
          saveState();
          render();
        });
      });

      document.getElementById('state-region-filter')?.addEventListener('change', (e) => {
        AppState.stateRegion = e.target.value;
        render();
      });

      document.getElementById('park-search-input')?.addEventListener('input', (e) => {
        AppState.parkSearch = e.target.value;
        render();
      });

      document.querySelectorAll('.park-visit-toggle').forEach(btn => {
        btn.addEventListener('click', () => {
          const pkId = btn.dataset.park;
          const k = `${AppState.activeProfileId}_${pkId}`;
          const current = AppState.records[k] || {};
          AppState.records[k] = { visited: !current.visited, wishlist: false };
          showToast(!current.visited ? 'Park marked as visited!' : 'Park visit removed.');
          saveState();
          render();
        });
      });

      document.querySelectorAll('.passport-subtab-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          AppState.passportSubTab = btn.dataset.sub;
          render();
        });
      });

      document.getElementById('open-stamp-modal')?.addEventListener('click', () => { AppState.showAddStampModal = true; render(); });
      document.getElementById('empty-stamp-btn')?.addEventListener('click', () => { AppState.showAddStampModal = true; render(); });
      document.getElementById('open-badge-modal')?.addEventListener('click', () => { AppState.showAddBadgeModal = true; render(); });
      document.getElementById('empty-badge-btn')?.addEventListener('click', () => { AppState.showAddBadgeModal = true; render(); });
      document.getElementById('open-trip-modal')?.addEventListener('click', () => { AppState.showAddTripModal = true; render(); });
      document.getElementById('empty-trip-btn')?.addEventListener('click', () => { AppState.showAddTripModal = true; render(); });

      document.getElementById('google-auth-select')?.addEventListener('change', (e) => {
        AppState.googleAuthIndex = e.target.value;
        saveState();
        showToast(`Google Maps account index set to ${e.target.value}!`);
      });

      document.getElementById('btn-launch-map')?.addEventListener('click', () => {
        const dest = document.getElementById('quick-map-park')?.value || 'Yellowstone National Park';
        window.open(`https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(dest)}&authuser=${AppState.googleAuthIndex}`, '_blank');
      });

      document.getElementById('btn-add-profile')?.addEventListener('click', () => { AppState.showAddProfileModal = true; render(); });
      document.getElementById('btn-open-delete-account')?.addEventListener('click', () => { AppState.showDeleteAccountModal = true; render(); });

      document.querySelectorAll('.delete-profile-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          const id = btn.dataset.id;
          AppState.profiles = AppState.profiles.filter(p => p.id !== id);
          if (AppState.activeProfileId === id) AppState.activeProfileId = 'p_primary';
          saveState();
          render();
        });
      });

      document.querySelectorAll('.delete-stamp-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          AppState.stamps = AppState.stamps.filter(s => s.id !== btn.dataset.id);
          saveState();
          render();
        });
      });

      document.querySelectorAll('.delete-badge-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          AppState.badges = AppState.badges.filter(b => b.id !== btn.dataset.id);
          saveState();
          render();
        });
      });

      document.querySelectorAll('.delete-trip-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          AppState.trips = AppState.trips.filter(t => t.id !== btn.dataset.id);
          saveState();
          render();
        });
      });

      bindModalSubmits();
    }

    function bindModalSubmits() {
      document.getElementById('close-edit-name')?.addEventListener('click', () => { AppState.showEditNameModal = false; render(); });
      document.querySelectorAll('.pick-avatar-btn').forEach(b => {
        b.addEventListener('click', () => {
          const active = getActiveProfile();
          active.avatar = b.dataset.avatar;
          saveState();
          render();
        });
      });
      document.getElementById('edit-name-form')?.addEventListener('submit', (e) => {
        e.preventDefault();
        const active = getActiveProfile();
        active.name = document.getElementById('edit-name-input')?.value.trim() || active.name;
        AppState.showEditNameModal = false;
        saveState();
        showToast('Profile updated!');
        render();
      });

      document.getElementById('close-add-profile')?.addEventListener('click', () => { AppState.showAddProfileModal = false; render(); });
      document.getElementById('add-profile-form')?.addEventListener('submit', (e) => {
        e.preventDefault();
        const name = (document.getElementById('new-profile-name')?.value || '').trim();
        const isKid = !!document.getElementById('new-profile-iskid')?.checked;
        if (name) {
          const newId = 'p_' + Date.now();
          AppState.profiles.push({ id: newId, name, avatar: isKid ? '🧒' : '🎒', color: '#10b981', isKid });
          AppState.activeProfileId = newId;
          AppState.showAddProfileModal = false;
          saveState();
          showToast(`Traveler ${name} added!`);
          render();
        }
      });

      document.getElementById('close-stamp-modal')?.addEventListener('click', () => { AppState.showAddStampModal = false; render(); });
      document.getElementById('add-stamp-form')?.addEventListener('submit', (e) => {
        e.preventDefault();
        const selVal = document.getElementById('stamp-park-select')?.value || 'Acadia|ME';
        const [parkName, state] = selVal.split('|');
        const newStamp = {
          id: 'st_' + Date.now(),
          profileId: AppState.activeProfileId,
          parkName,
          state,
          date: document.getElementById('stamp-date')?.value || new Date().toISOString().split('T')[0],
          inkColor: document.getElementById('stamp-color')?.value || '#10b981',
          location: (document.getElementById('stamp-loc')?.value || '').trim(),
          notes: (document.getElementById('stamp-notes')?.value || '').trim()
        };
        AppState.stamps.unshift(newStamp);
        AppState.showAddStampModal = false;
        saveState();
        showToast('Cancellation stamp logged!');
        render();
      });

      document.getElementById('close-badge-modal')?.addEventListener('click', () => { AppState.showAddBadgeModal = false; render(); });
      document.getElementById('add-badge-form')?.addEventListener('submit', (e) => {
        e.preventDefault();
        const selVal = document.getElementById('badge-park-select')?.value || 'Acadia|ME';
        const [parkName, state] = selVal.split('|');
        const newBadge = {
          id: 'bg_' + Date.now(),
          profileId: AppState.activeProfileId,
          parkName,
          state,
          date: document.getElementById('badge-date')?.value || new Date().toISOString().split('T')[0],
          badgeType: document.getElementById('badge-type')?.value || 'Wooden Shield',
          rangerName: (document.getElementById('badge-ranger')?.value || '').trim(),
          bookletDone: !!document.getElementById('badge-booklet')?.checked
        };
        AppState.badges.unshift(newBadge);
        AppState.showAddBadgeModal = false;
        saveState();
        showToast('Junior Ranger badge added!');
        render();
      });

      document.getElementById('close-trip-modal')?.addEventListener('click', () => { AppState.showAddTripModal = false; render(); });
      document.getElementById('add-trip-form')?.addEventListener('submit', (e) => {
        e.preventDefault();
        const newTrip = {
          id: 'tr_' + Date.now(),
          title: (document.getElementById('trip-title')?.value || '').trim(),
          destination: document.getElementById('trip-dest')?.value || 'Yellowstone National Park',
          startDate: document.getElementById('trip-start')?.value || '',
          endDate: document.getElementById('trip-end')?.value || '',
          notes: (document.getElementById('trip-notes')?.value || '').trim()
        };
        AppState.trips.unshift(newTrip);
        AppState.showAddTripModal = false;
        saveState();
        showToast('Trip itinerary created!');
        render();
      });

      document.getElementById('close-delete-modal')?.addEventListener('click', () => { AppState.showDeleteAccountModal = false; render(); });
      const delInput = document.getElementById('delete-confirm-input');
      const delBtn = document.getElementById('confirm-delete-btn');
      if (delInput && delBtn) {
        delInput.addEventListener('input', (e) => {
          if (e.target.value.trim().toUpperCase() === 'DELETE') {
            delBtn.disabled = false;
            delBtn.classList.remove('opacity-50', 'cursor-not-allowed');
          } else {
            delBtn.disabled = true;
            delBtn.classList.add('opacity-50', 'cursor-not-allowed');
          }
        });
        delBtn.addEventListener('click', () => {
          localStorage.clear();
          location.reload();
        });
      }
    }

    if (document.readyState === 'loading') {
      document.addEventListener('DOMContentLoaded', render);
    } else {
      render();
    }
  </script>
</body>
</html>
