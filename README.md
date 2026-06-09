# Aegis — base communautaire

Base **anti-démarchage / anti-arnaque** de l'application [Aegis](https://cyberlogic91-dev.github.io/aegis-app/).

Elle ne contient **aucun numéro de téléphone en clair** : uniquement des **empreintes SHA-256**
de numéros normalisés (format international `+33…`). Impossible de retrouver un numéro à partir
de son empreinte — c'est conçu **RGPD by design**.

## Fichier publié

- [`community_blacklist.json`](community_blacklist.json) — la base lue par l'application.
  Servie en lecture seule via l'URL *raw* :
  `https://raw.githubusercontent.com/Cyberlogic91-dev/aegis-community/main/community_blacklist.json`

Format :

```json
{
  "synced_at": 1781400000000,
  "entries": [
    { "phone_hash": "a1b2…", "category": "TELEMARKETING", "report_count": 5, "label": null, "deleted": false }
  ]
}
```

## Signaler un numéro

Deux façons :

1. **Depuis l'app Aegis** : sur un appel, *Signaler* → l'app propose de créer un signalement
   communautaire pré-rempli (empreinte + catégorie).
2. **Manuellement** : [ouvrir un signalement](../../issues/new?template=report.yml) et coller
   l'empreinte SHA-256 affichée par l'app.

### Anti-abus

Un numéro **n'entre dans la base que lorsqu'il a été signalé par au moins 3 personnes
distinctes** (seuil configurable). Un mainteneur peut forcer l'ajout immédiat en posant le
label `verified` sur un signalement vérifié. L'agrégation est automatique
([`.github/workflows/aggregate.yml`](.github/workflows/aggregate.yml)).

Aucune donnée personnelle n'est acceptée : tout signalement contenant un numéro en clair ou
des informations nominatives sera rejeté.

### Révocation (faux positif)

Si un numéro légitime a été ajouté par erreur, un mainteneur pose le label **`false-positive`**
sur une issue contenant l'empreinte concernée. À la prochaine agrégation, l'entrée est marquée
`"deleted": true` : les applications la **retirent** automatiquement de leur base locale.

### Modération — file d'attente

L'agrégateur publie aussi [`pending.json`](pending.json) : la liste des empreintes ayant reçu
**1 ou 2 signalements** (sous le seuil), avec le nombre de signaleurs manquants. Pratique pour
suivre ce qui « monte » et décider d'une validation anticipée (`verified`).

Le fichier `community_blacklist.json` est **trié par empreinte** et n'est ré-écrit que lorsque
son contenu change réellement (pas de commit inutile à chaque exécution planifiée).

## Licence

Données sous licence libre (domaine public / CC0 pour les empreintes agrégées). Le code
d'agrégation est sous licence MIT.
