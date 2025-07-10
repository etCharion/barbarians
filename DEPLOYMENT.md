# Návod pro nasazení aplikace

## GitHub Pages

### Automatické nasazení pomocí GitHub Actions

1. **Přidejte GitHub Actions workflow:**
   
   Vytvořte soubor `.github/workflows/deploy.yml`:

   ```yaml
   name: Deploy to GitHub Pages

   on:
     push:
       branches: [ main ]
     pull_request:
       branches: [ main ]

   jobs:
     build-and-deploy:
       runs-on: ubuntu-latest
       
       steps:
       - name: Checkout
         uses: actions/checkout@v3

       - name: Setup Node.js
         uses: actions/setup-node@v3
         with:
           node-version: '18'
           cache: 'npm'

       - name: Install dependencies
         run: npm ci

       - name: Build
         run: npm run build

       - name: Deploy to GitHub Pages
         uses: peaceiris/actions-gh-pages@v3
         if: github.ref == 'refs/heads/main'
         with:
           github_token: ${{ secrets.GITHUB_TOKEN }}
           publish_dir: ./dist
   ```

2. **Nastavte GitHub Pages:**
   - Jděte do Settings → Pages
   - Zvolte "Deploy from a branch"
   - Vyberte branch `gh-pages`

3. **Upravte vite.config.js pro GitHub Pages:**
   ```javascript
   export default defineConfig({
     plugins: [react()],
     base: '/barbarians-at-the-gates-rules/', // název vašeho repozitáře
   })
   ```

### Manuální nasazení

1. **Build aplikace:**
   ```bash
   npm run build
   ```

2. **Nasaďte obsah dist/ složky na GitHub Pages**

## Netlify

1. **Připojte GitHub repozitář k Netlify**
2. **Nastavte build příkazy:**
   - Build command: `npm run build`
   - Publish directory: `dist`
3. **Automatické nasazení při push do main branch**

## Vercel

1. **Importujte projekt z GitHubu**
2. **Vercel automaticky detekuje Vite projekt**
3. **Automatické nasazení při každém commitu**

## Vlastní server

### Nginx konfigurace

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /path/to/dist;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Gzip komprese
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Cache statických souborů
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

### Apache konfigurace

Vytvořte `.htaccess` soubor v dist/ složce:

```apache
RewriteEngine On
RewriteBase /

# Handle Angular and other front-end routes
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.html [L]

# Gzip compression
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/plain
    AddOutputFilterByType DEFLATE text/html
    AddOutputFilterByType DEFLATE text/xml
    AddOutputFilterByType DEFLATE text/css
    AddOutputFilterByType DEFLATE application/xml
    AddOutputFilterByType DEFLATE application/xhtml+xml
    AddOutputFilterByType DEFLATE application/rss+xml
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE application/x-javascript
</IfModule>

# Cache static files
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresByType text/css "access plus 1 year"
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/jpg "access plus 1 year"
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType image/gif "access plus 1 year"
    ExpiresByType image/svg+xml "access plus 1 year"
</IfModule>
```

## Optimalizace pro produkci

### 1. Optimalizace obrázků
```bash
# Instalace nástroje pro optimalizaci obrázků
npm install -D imagemin imagemin-webp imagemin-mozjpeg imagemin-pngquant

# Skript pro optimalizaci
npm run optimize-images
```

### 2. Bundle analýza
```bash
npm install -D rollup-plugin-visualizer
npm run build
npm run analyze
```

### 3. Service Worker pro offline podporu
```bash
npm install -D vite-plugin-pwa
```

## Monitoring a analytics

### Google Analytics
Přidejte do `index.html`:
```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'GA_MEASUREMENT_ID');
</script>
```

### Sentry pro error tracking
```bash
npm install @sentry/react @sentry/tracing
```

## Bezpečnost

1. **Content Security Policy (CSP)**
2. **HTTPS certifikát**
3. **Security headers**
4. **Regular dependency updates**

## Výkon

1. **Lazy loading komponentů**
2. **Code splitting**
3. **Image optimization**
4. **CDN pro statické soubory**
5. **Gzip/Brotli komprese**

