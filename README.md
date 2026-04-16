# API Specification Templates v2 - Conformes aux Meilleures Pratiques MuleSoft

## Vue d'ensemble

Ce repository contient deux assets conformes aux meilleures pratiques MuleSoft et aux règles de gouvernance d'API :

1. **API Common Library v2** - Bibliothèque de composants réutilisables
2. **API Specification Template v2** - Template d'API REST complet

Ces assets sont conçus pour respecter le ruleset **"Anypoint Best Practices v1.6.3"** et fournir une base solide pour le développement d'APIs robustes, sécurisées et bien documentées.

## 🎯 Objectifs

- ✅ **Conformité** : Respect des standards MuleSoft et des règles de gouvernance
- ✅ **Réutilisabilité** : Composants standardisés pour accélérer le développement
- ✅ **Sécurité** : Implémentation OAuth 2.0 et gestion des permissions
- ✅ **Observabilité** : Traceabilité, monitoring et health checks intégrés
- ✅ **Documentation** : Spécifications complètes et exemples d'utilisation

## 📁 Structure du Projet

```
workspace/
├── api-common-library-v2/           # Bibliothèque commune
│   ├── api-common-library-v2.raml   # Définitions RAML (types, traits)
│   └── exchange.json                 # Métadonnées Exchange
├── api-specification-template-v2/    # Template API
│   ├── api-specification-template-v2.raml  # Spécification API complète
│   └── exchange.json                 # Métadonnées Exchange
└── README.md                         # Cette documentation
```

## 📚 API Common Library v2

### Contenu

La bibliothèque commune (`api-common-library-v2.raml`) fournit :

#### Types de Données Standardisés
- **UUID** : Identifiants uniques conformes RFC 4122
- **DateTime** : Dates au format ISO 8601 / RFC 3339
- **Metadata** : Métadonnées d'audit (createdAt, updatedAt, version)
- **PaginationRequest/Response** : Gestion de la pagination
- **ErrorResponse** : Format d'erreur structuré et consistent
- **Address** : Types d'adresse standardisés
- **Contact** : Types de contact avec validation

#### Traits de Sécurité
- **oauth2Protected** : Authentification OAuth 2.0 Bearer Token
- **apiKeyProtected** : Authentification par clé API
- **rateLimited** : Limitation de taux avec headers informatifs
- **traceable** : Headers de corrélation et traçabilité
- **conditionalRequest** : Validation conditionnelle avec ETag

#### Traits Fonctionnels
- **paginated** : Pagination avec offset/limit
- **sortable** : Tri multi-critères
- **filterable** : Filtrage flexible
- **cacheable** : Contrôle de cache HTTP
- **standardResponses** : Réponses d'erreur standardisées

### Utilisation

```raml
#%RAML 1.0
uses:
  common: path/to/api-common-library-v2.raml

types:
  Customer:
    type: object
    properties:
      id: common.UUID
      metadata: common.Metadata

/customers:
  get:
    is: 
      - common.oauth2Protected
      - common.paginated
      - common.traceable
```

## 🚀 API Specification Template v2

### Caractéristiques

L'API template (`api-specification-template-v2.raml`) implémente :

#### Sécurité
- **OAuth 2.0** avec Bearer Token
- **Rate Limiting** (1000 req/heure par défaut)
- **Headers de traçabilité** pour l'observabilité
- **Gestion de concurrence optimiste** avec ETag

#### Opérations CRUD Complètes
- **GET /customers** : Liste paginée avec tri et filtrage
- **POST /customers** : Création avec validation
- **GET /customers/{id}** : Récupération avec cache conditionnel
- **PUT /customers/{id}** : Mise à jour complète
- **PATCH /customers/{id}** : Mise à jour partielle
- **DELETE /customers/{id}** : Suppression avec vérifications

#### Monitoring et Observabilité
- **GET /health** : Health check pour load balancers
- **GET /metrics** : Métriques Prometheus
- **Corrélation IDs** : Suivi des requêtes end-to-end
- **Structured Logging** : Format d'erreur consistant

#### Documentation
- **Exemples détaillés** : Requêtes et réponses complètes
- **Descriptions techniques** : Guide d'implémentation
- **Standards respectés** : Conformité aux meilleures pratiques

### Exemple d'Utilisation

```bash
# Authentification
curl -H "Authorization: Bearer <token>" \
     -H "X-Correlation-ID: 123e4567-e89b-12d3-a456-426614174000" \
     https://api.example.com/customers

# Création d'un client
curl -X POST \
     -H "Authorization: Bearer <token>" \
     -H "Content-Type: application/json" \
     -d '{"name":"Jean Dupont","email":"jean@example.com"}' \
     https://api.example.com/customers

# Pagination et tri
curl -H "Authorization: Bearer <token>" \
     "https://api.example.com/customers?offset=0&limit=20&sort=name:asc"
```

## 🛡️ Meilleures Pratiques Implémentées

### Sécurité
- ✅ OAuth 2.0 Bearer Token obligatoire
- ✅ Validation stricte des formats de données
- ✅ Rate limiting avec headers informatifs
- ✅ Gestion des erreurs sécurisée (pas de fuite d'info)

### Performance
- ✅ Pagination obligatoire pour les collections
- ✅ Cache HTTP avec ETag et Last-Modified
- ✅ Requêtes conditionnelles (If-Match, If-None-Match)
- ✅ Compression et optimisation des réponses

### Observabilité
- ✅ Correlation IDs sur toutes les requêtes
- ✅ Health checks standardisés
- ✅ Métriques Prometheus
- ✅ Structured error responses

### Documentation
- ✅ Descriptions complètes des endpoints
- ✅ Exemples de requêtes/réponses
- ✅ Codes d'erreur documentés
- ✅ Guide d'utilisation intégré

## 🔧 Utilisation des Templates

### 1. Utiliser la Bibliothèque Commune

```raml
#%RAML 1.0
title: Mon API

uses:
  common: ./api-common-library-v2/api-common-library-v2.raml

types:
  MonType:
    type: object
    properties:
      id: common.UUID
      metadata: common.Metadata

/mon-endpoint:
  get:
    is: [common.oauth2Protected, common.paginated]
```

### 2. Adapter le Template API

1. **Copier** le template API
2. **Remplacer** "Customer" par votre entité métier
3. **Adapter** les propriétés et validations
4. **Configurer** les endpoints selon vos besoins
5. **Tester** la conformité aux règles de gouvernance

### 3. Validation et Publication

```bash
# Validation locale
raml-validate api-specification-template-v2.raml

# Publication sur Exchange (nécessite anypoint-cli)
anypoint-cli api-mgr api import api-specification-template-v2.raml
```

## 📋 Checklist de Conformité

Avant de publier votre API, vérifiez :

### Sécurité
- [ ] OAuth 2.0 configuré sur tous les endpoints sensibles
- [ ] Rate limiting configuré selon les besoins
- [ ] Validation stricte des inputs
- [ ] Gestion d'erreur sécurisée

### Performance
- [ ] Pagination sur les collections
- [ ] Cache HTTP configuré
- [ ] Requêtes conditionnelles supportées
- [ ] Tri et filtrage disponibles

### Observabilité
- [ ] Health check implémenté
- [ ] Correlation IDs utilisés
- [ ] Métriques exposées
- [ ] Logging structuré

### Documentation
- [ ] Tous les endpoints documentés
- [ ] Exemples fournis
- [ ] Codes d'erreur expliqués
- [ ] Guide d'utilisation complet

## 🚦 Règles de Gouvernance

Ces templates respectent les règles suivantes :

1. **Anypoint Best Practices v1.6.3**
2. **API Documentation Best Practices**
3. **Authentication Security Best Practices**
4. **OpenAPI Best Practices** (applicable à RAML)

## 🤝 Support et Contribution

- **Équipe Support** : API Governance Team
- **Email** : api-governance@organization.com  
- **Documentation** : https://confluence.organization.com/api-governance

## 📄 Licence

Proprietary - Usage interne uniquement selon les termes de la licence organisationnelle.

---

## 🔄 Versions

- **v2.0.0** : Version initiale conforme aux meilleures pratiques
- **v1.x.x** : Versions legacy (non conformes)

## ⚡ Quick Start

1. Cloner les templates
2. Adapter selon vos besoins
3. Valider la conformité
4. Publier sur Exchange
5. Implémenter avec MuleSoft

**Prêt à développer des APIs de qualité production ! 🎉**