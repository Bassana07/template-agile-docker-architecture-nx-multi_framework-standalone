# template-agile-docker-architecture-nx-multi-frameworkstandalone

# My Company - Monorepo Frontend

Architecture micro-frontends avec Nx, Next.js et Docker pour une application web modulaire et scalable.

---

## 🛠️ Technologies

- **Framework**: [Nx](https://nx.dev/) - Monorepo tooling
- **Frontend**: [Next.js 14](https://nextjs.org/) - React framework
- **Module Federation**: Webpack Module Federation
- **Styling**: Tailwind CSS
- **Language**: TypeScript
- **Containerization**: Docker & Docker Compose
- **Reverse Proxy**: Nginx

---

## 📦 Prérequis

- **Node.js**: v18.x ou supérieur
- **npm**: v9.x ou supérieur
- **Docker**: v24.x ou supérieur
- **Docker Compose**: v2.x ou supérieur

### Vérifier les versions
```bash
node --version    # v18.x.x
npm --version     # 9.x.x
docker --version  # 24.x.x
docker-compose --version  # 2.x.x
```

---

### Configuration des variables d'environnement

Créer les fichiers `.env` pour chaque environnement :
```bash
# Development
cp .env.example .env.development

# Production
cp .env.example .env.production
```
## 💻 Développement

### Développement local (sans Docker)

#### Lancer toutes les applications
```bash
# Lancer shell + tous les MFE en parallèle
npm run dev

# OU avec Nx directement
nx run-many --target=serve --projects=web-client-shell,web-client-auth,web-client-products,web-client-orders,web-client-checkout --parallel=5
```

#### Lancer une application spécifique
```bash
# Shell uniquement
nx serve web-client-shell

# Auth MFE uniquement
nx serve web-client-auth

# Products MFE uniquement
nx serve web-client-products
```

#### Accéder aux applications

- **Shell**: http://localhost:4200

### Build
```bash
# Build toutes les apps
npm run build

# Build une app spécifique
nx build web-client-shell --prod

# Build seulement ce qui a changé
nx affected:build --base=main
```

### Tests
```bash
# Lancer tous les tests
npm test

# Tests d'une app spécifique
nx test web-client-auth

# Tests E2E
nx e2e web-client-shell-e2e

# Tests affectés par les changements
nx affected:test --base=main
```

### Lint
```bash
# Lint tout
npm run lint

# Lint une app spécifique
nx lint web-client-shell

# Lint avec auto-fix
nx lint web-client-shell --fix
```

---

## 🐳 Docker

### Développement avec Docker

#### Lancer tous les services
```bash
# Démarrer en mode dev (avec hot reload)
docker-compose --profile dev up

# En arrière-plan
docker-compose --profile dev up -d

# Avec rebuild des images
docker-compose --profile dev up --build
```

#### Lancer des services spécifiques
```bash
# Shell + Auth uniquement
docker-compose --profile dev up web-client-shell-dev web-client-auth-dev

# Products + Orders uniquement
docker-compose --profile dev up web-client-products-dev web-client-orders-dev
```

#### Accéder aux applications (Docker Dev)

- **Shell**: http://localhost:3000

#### Voir les logs
```bash
# Tous les services
docker-compose --profile dev logs -f

# Service spécifique
docker-compose --profile dev logs -f web-client-shell-dev

# Dernières 100 lignes
docker-compose --profile dev logs --tail=100 web-client-auth-dev
```

#### Arrêter les services
```bash
# Arrêter
docker-compose --profile dev down

# Arrêter et supprimer les volumes
docker-compose --profile dev down -v
```

---

### Production avec Docker

#### Build et lancer
```bash
# Build et démarrer
docker-compose --profile prod up --build

# En arrière-plan
docker-compose --profile prod up -d
```

#### Rebuild un service spécifique
```bash
# Rebuild seulement products
docker-compose --profile prod build web-client-products-prod

# Redémarrer products
docker-compose --profile prod up -d web-client-products-prod
```

#### Accéder aux applications (Docker Prod)

- **Shell**: http://localhost:3000
- **Nginx**: http://localhost:80

---

## 🚢 Déploiement

### Déploiement manuel

#### 1. Build les images
```bash
docker-compose --profile prod build
```
#### 4. Déployer sur le serveur
```bash
ssh user@production-server << 'EOF'
  cd /opt/myapp
  docker-compose --profile prod pull
  docker-compose --profile prod up -d
EOF
```

### Déploiement avec CI/CD (GitHub Actions)

Le workflow CI/CD est configuré dans `.github/workflows/ci-cd.yml`
```bash
# Déployer automatiquement
git push origin main

# Déployer manuellement un service spécifique
# Via GitHub Actions UI: Actions > Deploy > Run workflow
# Choisir le service: web-client-products
```

---

## 📁 Structure du projet
```
my-company/
├── apps/
│   ├── web-client/                   # Shell principal
│   │   ├── app/                      # Next.js App Router
│   │   │   ├── layout.tsx            # Layout global
│   │   │   ├── page.tsx              # Page d'accueil
│   │   │   ├── auth/                 # Routes auth (charge MFE)
│   │   │   ├── products/             # Routes products (charge MFE)
│   │   │   ├── orders/               # Routes orders (charge MFE)
│   │   │   └── checkout/             # Routes checkout (charge MFE)
│   │   ├── components/
│   │   │   ├── Header.tsx
│   │   │   └── Footer.tsx
│   │   ├── module-federation.config.ts
│   │   ├── next.config.js
│   │   ├── Dockerfile                # Production
│   │   ├── Dockerfile.dev            # Development
│   │   └── project.json
│
├── libs/
│   └── shared/
│       ├── ui/                        # Design system
│       │   ├── src/
│       │   │   ├── lib/
│       │   │   │   ├── button/
│       │   │   │   ├── card/
│       │   │   │   └── modal/
│       │   │   └── index.ts
│       │   └── project.json
│       │
│       
│
├── .github/
│   └── workflows/
│       └── ci-cd.yml                 # CI/CD
│
├── docker-compose.yml                 # Orchestration Docker
├── .dockerignore
├── .gitignore
├── nx.json                            # Configuration Nx
├── package.json
├── tsconfig.base.json
└── README.md
```

---

## 🔧 Commandes utiles

### Nx
```bash
# Voir le graphe de dépendances
nx graph

# Voir le graphe d'un projet spécifique
nx graph --focus=web-client-shell

# Lister tous les projets
nx list

# Générer une nouvelle app Next.js
nx g @nx/next:app my-new-app

# Générer une nouvelle library
nx g @nx/react:lib my-lib --directory=shared

# Générer un composant
nx g @nx/react:component Button --project=shared-ui

# Générer une page Next.js
nx g @nx/next:page about --project=web-client-shell

# Reset le cache Nx
nx reset

# Voir les apps affectées par vos changements
nx affected:apps --base=main

# Build seulement ce qui est affecté
nx affected:build --base=main

# Test seulement ce qui est affecté
nx affected:test --base=main
```

### Docker
```bash
# Voir les containers en cours
docker-compose ps

# Exec dans un container
docker-compose --profile dev exec web-client-shell-dev sh

# Voir les ressources utilisées
docker stats

# Nettoyer les images inutilisées
docker system prune -a

# Rebuild sans cache
docker-compose --profile dev build --no-cache web-client-shell-dev

# Redémarrer un service
docker-compose --profile dev restart web-client-products-dev
```

### npm
```bash
# Installer une nouvelle dépendance
npm install --save package-name

# Installer une dépendance de dev
npm install --save-dev package-name

# Mettre à jour les dépendances
npm update

# Vérifier les dépendances obsolètes
npm outdated

# Audit de sécurité
npm audit

# Fix les vulnérabilités
npm audit fix

```

---

## ✅ Bonnes pratiques

### 1. Organisation du code

- **Feature-based**: Organiser par fonctionnalité, pas par type de fichier
- **Barrel exports**: Utiliser `index.ts` pour les exports publics
- **Imports absolus**: Utiliser les path aliases (`@mycompany/shared/ui`)

### 2. Module Federation

- **SSR désactivé**: Toujours `ssr: false` pour les MFE chargés dynamiquement
- **Loading states**: Fournir des composants de loading
- **Error boundaries**: Gérer les erreurs de chargement des MFE

### 3. API Calls

- **Centralisé**: Tous les appels API dans `libs/shared/api-client`
- **Error handling**: Gestion centralisée des erreurs
- **Types**: Toujours typer les réponses API

### 4. Git Workflow
```bash
# 1. Créer une branche feature
git checkout -b feature/add-product-filters

# 2. Faire vos modifications
# ...

# 3. Commit avec message conventionnel
git commit -m "feat(products): add filters for product list"

# 4. Push et créer une PR
git push origin feature/add-product-filters
```
### 5. Code Review

- ✅ Tests passent
- ✅ Lint passe
- ✅ Build réussit
- ✅ Pas de code dupliqué
- ✅ Types TypeScript corrects
- ✅ Documentation à jour

---

## 🐛 Troubleshooting

### Problème: Module Federation ne charge pas les MFE

**Symptôme**: Erreur "Cannot find module 'mfe-auth/LoginPage'"

**Solution**:
```bash
# 1. Vérifier que tous les MFE sont démarrés
nx run-many --target=serve --all

# 2. Vérifier les URLs dans module-federation.config.ts
# 3. Clear cache Nx
nx reset

# 4. Redémarrer
```

---

### Problème: Hot reload ne fonctionne pas en Docker

**Symptôme**: Changements de code non reflétés

**Solution**:
```bash
# Vérifier que les volumes sont bien montés
docker-compose --profile dev config

# Redémarrer les containers
docker-compose --profile dev restart
```
---

## 📚 Ressources

### Documentation

- [Nx Documentation](https://nx.dev/getting-started/intro)
- [Next.js Documentation](https://nextjs.org/docs)
- [Module Federation](https://webpack.js.org/concepts/module-federation/)
- [Docker Documentation](https://docs.docker.com/)

### Tutoriels

- [Nx Micro-Frontends Tutorial](https://nx.dev/recipes/module-federation/micro-frontend-architecture)
- [Next.js Learn](https://nextjs.org/learn)

---

## 📄 License

Copyright © 2025 My Company. All rights reserved.