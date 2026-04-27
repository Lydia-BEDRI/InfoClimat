# Security Policy

## À propos de ce projet

Ce projet est un **projet forké pédagogique** développé dans le cadre du module **Infrastructure et DevOps** à l'**ESGI** (École Supérieure de Génie Informatique) en collaboration avec **Data For Good** (Saison 14).

## Reporting a Vulnerability

Si vous découvrez une vulnérabilité de sécurité, veuillez nous la signaler par email à `l.bedri@myskolae.fr` plutôt que d'utiliser le tracker de problèmes public.

Veuillez inclure:

- Description de la vulnérabilité
- Étapes pour la reproduire
- Impact potentiel
- Versions affectées

Nous nous engageons à:

- Accuser réception dans les 48 heures
- Fournir une évaluation dans les 5 jours
- Publier un correctif rapidement après confirmation

## Versions supportées

| Version | Support |
| ------- | ------- |
| 1.0.x   | ✅ LTS  |

## Bonnes pratiques de sécurité

- Utilisez les versions à jour de Python (3.11+) et Node.js (24+)
- Utilisez les images Docker hardened depuis `dhi.io`
- Exécutez `npm audit` et `pip audit` régulièrement
- Vérifiez les dépendances avant d'ajouter des packages
