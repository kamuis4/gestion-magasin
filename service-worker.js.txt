// Cache très simple pour fonctionner hors-ligne sur GitHub Pages
const CACHE = 'mini-shop-v1';
const ASSETS = [
  './',
  './index.html',
  './manifest.webmanifest'
  // Ajoute ici 'icon-192.png', 'icon-512.png' si tu les utilises
];

self.addEventListener('install', (e) => {
  e.waitUntil(caches.open(CACHE).then((c) => c.addAll(ASSETS)));
});

self.addEventListener('activate', (e) => {
  e.waitUntil(
    caches.keys().then((keys) =>
      Promise.all(keys.filter(k => k !== CACHE).map(k => caches.delete(k)))
    )
  );
});

self.addEventListener('fetch', (e) => {
  e.respondWith(
    caches.match(e.request).then((r) => r || fetch(e.request).then((resp) => {
      // Optionnel: mise en cache progressif
      const copy = resp.clone();
      caches.open(CACHE).then((c) => c.put(e.request, copy)).catch(()=>{});
      return resp;
    }).catch(() => caches.match('./index.html')))
  );
});
