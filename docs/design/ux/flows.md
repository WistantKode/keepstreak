# User Journey Flows

## 1. "Le Handshake Magique" (Onboarding/Setup)
Ce parcours est le cœur de la confiance. Il transforme une prouesse technique complexe en une expérience fluide de moins de 60 secondes.

```mermaid
graph TD
    A[Landing Page: "Get Started"] --> B(OAuth GitHub)
    B --> C{Shadow Provisioning}
    C -->|Auto| D[Creation of .keepstreak-pulse Repo]
    D --> E[Injection of HMAC Secrets]
    E --> F[Verification Pulse]
    F --> G[Dashboard Actionable]
    
    style G fill:#3b82f6,stroke:#fff,stroke-width:2px
```

## 2. "Le Monitoring de l'Ombre" (Dashboard)
Une fois activé, l'utilisateur doit ressentir la "vie" de son automation sans être submergé.
- **Point d'entrée :** Vue d'ensemble du Dashboard.
- **Moment de succès :** Confirmation visuelle que le Shadow Worker opère de manière autonome et sécurisée.

## 3. "La Preuve Technique" (Recruteur/Expert)
Comment le système prouve son excellence à un tiers sans accès aux comptes GitHub.
- **Parcours :** Partage d'un lien "Proof of Security" ou consultation de la documentation publique générée.
- **Résultat :** Validation du schéma HMAC et de l'architecture sans token.

---
[⬅️ Retour à l'Index UX](index.md)
