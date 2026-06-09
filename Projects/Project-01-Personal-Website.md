# 🌐 Project 1: Personal Portfolio Website

**Difficulty:** Beginner | **Time:** 4-6 hours | **Module:** 01-07

---

## 🎯 Project Goal

Ek personal portfolio website banao aur Linux server pe deploy karo (Nginx + static files).

---

## 📋 What You Will Build

```
yourdomain.com
    ├── Home page (About me)
    ├── Projects section
    ├── Skills section
    └── Contact section
```

---

## 🛠️ Tech Stack

- HTML, CSS, JavaScript (static)
- Nginx web server
- Linux server
- Let's Encrypt SSL

---

## 📝 Step-by-Step

### Step 1: Project Structure

```bash
mkdir -p ~/portfolio/{css,js,images}
cd ~/portfolio

touch index.html css/style.css js/main.js
```

### Step 2: HTML banao

```bash
cat > index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Portfolio | Linux Developer</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <nav>
        <h1>Your Name</h1>
        <ul>
            <li><a href="#about">About</a></li>
            <li><a href="#projects">Projects</a></li>
            <li><a href="#skills">Skills</a></li>
            <li><a href="#contact">Contact</a></li>
        </ul>
    </nav>

    <section id="hero">
        <h2>Full Stack Python Developer</h2>
        <p>Building apps with Python, Flask, Linux & Docker</p>
        <a href="#projects" class="btn">See My Work</a>
    </section>

    <section id="skills">
        <h2>Skills</h2>
        <div class="skills-grid">
            <div class="skill">🐍 Python</div>
            <div class="skill">🌐 Flask/FastAPI</div>
            <div class="skill">🐧 Linux</div>
            <div class="skill">🐳 Docker</div>
            <div class="skill">☁️ Cloud (Azure/AWS)</div>
            <div class="skill">🗃️ PostgreSQL</div>
        </div>
    </section>

    <section id="projects">
        <h2>Projects</h2>
        <div class="projects-grid">
            <div class="project-card">
                <h3>Flask Blog</h3>
                <p>A full-featured blog with Flask, PostgreSQL, and Docker</p>
                <a href="#" class="btn-small">GitHub</a>
            </div>
            <div class="project-card">
                <h3>FastAPI REST API</h3>
                <p>RESTful API with authentication and auto-docs</p>
                <a href="#" class="btn-small">GitHub</a>
            </div>
        </div>
    </section>

    <section id="contact">
        <h2>Contact</h2>
        <p>Email: your@email.com</p>
        <p>GitHub: github.com/yourusername</p>
        <p>LinkedIn: linkedin.com/in/yourname</p>
    </section>

    <footer>
        <p>Built with ❤️ and deployed on Linux</p>
    </footer>
    <script src="js/main.js"></script>
</body>
</html>
EOF
```

### Step 3: CSS banao

```bash
cat > css/style.css << 'EOF'
* { margin: 0; padding: 0; box-sizing: border-box; }

body {
    font-family: 'Segoe UI', sans-serif;
    color: #333;
    line-height: 1.6;
}

nav {
    background: #2c3e50;
    color: white;
    padding: 1rem 2rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: sticky;
    top: 0;
}

nav ul { list-style: none; display: flex; gap: 2rem; }
nav a { color: white; text-decoration: none; }
nav a:hover { color: #3498db; }

#hero {
    background: linear-gradient(135deg, #2c3e50, #3498db);
    color: white;
    text-align: center;
    padding: 5rem 2rem;
}

#hero h2 { font-size: 2.5rem; margin-bottom: 1rem; }
#hero p { font-size: 1.2rem; margin-bottom: 2rem; }

.btn {
    background: white;
    color: #2c3e50;
    padding: 0.8rem 2rem;
    border-radius: 25px;
    text-decoration: none;
    font-weight: bold;
    transition: all 0.3s;
}

.btn:hover { background: #3498db; color: white; }

section { padding: 4rem 2rem; }
section h2 { text-align: center; font-size: 2rem; margin-bottom: 2rem; color: #2c3e50; }

.skills-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 1rem;
    max-width: 800px;
    margin: 0 auto;
}

.skill {
    background: #f8f9fa;
    padding: 1rem;
    border-radius: 10px;
    text-align: center;
    border: 2px solid #e9ecef;
    font-size: 1.1rem;
    transition: all 0.3s;
}

.skill:hover { background: #2c3e50; color: white; border-color: #2c3e50; }

.projects-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.5rem;
    max-width: 900px;
    margin: 0 auto;
}

.project-card {
    background: white;
    border-radius: 10px;
    padding: 1.5rem;
    box-shadow: 0 3px 15px rgba(0,0,0,0.1);
    transition: transform 0.3s;
}

.project-card:hover { transform: translateY(-5px); }
.project-card h3 { color: #2c3e50; margin-bottom: 0.5rem; }
.project-card p { color: #666; margin-bottom: 1rem; }

.btn-small {
    background: #3498db;
    color: white;
    padding: 0.4rem 1rem;
    border-radius: 5px;
    text-decoration: none;
    font-size: 0.9rem;
}

#contact { background: #f8f9fa; text-align: center; }
#contact p { font-size: 1.1rem; margin: 0.5rem; }

footer {
    background: #2c3e50;
    color: white;
    text-align: center;
    padding: 1rem;
}
EOF
```

### Step 4: JavaScript add karo

```bash
cat > js/main.js << 'EOF'
// Smooth scroll
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
    anchor.addEventListener('click', function(e) {
        e.preventDefault();
        document.querySelector(this.getAttribute('href')).scrollIntoView({
            behavior: 'smooth'
        });
    });
});

// Skill animation
const skills = document.querySelectorAll('.skill');
const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            entry.target.style.animation = 'fadeIn 0.5s ease forwards';
        }
    });
});
skills.forEach(skill => observer.observe(skill));

console.log("Portfolio loaded! Built with Linux 🐧");
EOF
```

### Step 5: Nginx pe Deploy karo

```bash
# Web root mein copy karo:
sudo mkdir -p /var/www/portfolio
sudo cp -r ~/portfolio/* /var/www/portfolio/
sudo chown -R www-data:www-data /var/www/portfolio

# Nginx config:
sudo tee /etc/nginx/sites-available/portfolio << 'NGINXEOF'
server {
    listen 80;
    server_name localhost;

    root /var/www/portfolio;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg)$ {
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
}
NGINXEOF

sudo ln -sf /etc/nginx/sites-available/portfolio /etc/nginx/sites-enabled/
sudo nginx -t && sudo service nginx reload 2>/dev/null || sudo systemctl reload nginx

# Test:
curl http://localhost | grep "Portfolio"
echo "Portfolio deployed!"
```

---

## ✅ Success Criteria

- [ ] HTML, CSS, JS sab files create ki hain
- [ ] Nginx se serve ho rahi hai
- [ ] curl http://localhost kaam karta hai
- [ ] Responsive design (mobile friendly)
- [ ] Sab sections visible hain

**Congratulations! Tumhara portfolio live hai! 🎉**
