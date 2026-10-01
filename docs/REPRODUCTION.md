# Reproduire la démonstration et les mesures

## 1. Installer — ne collecte rien

Node 22+, npm ; Chrome installé pour la configuration utilisée lors de la première série. Les commandes suivantes partent de la racine de ce dépôt.

```sh
npm ci
# Pour la vérification visuelle Firefox ou Chromium fourni par Playwright :
npx playwright install chromium firefox
```

Les versions de Playwright (1.63.0) et axe-core (4.10.3) sont figées dans le lockfile. Le module web-vitals 6.2.2 embarqué est vérifié par empreinte ; aucun CDN n’intervient à l’exécution. Les sources et la licence du paquet de référence sont conservées.

## 2. Consulter la présentation — aucun nouvel essai

```sh
npm run build
npm start
```

Ouvrir `http://127.0.0.1:4181/image-layout-stability-demo/`. `dist/` est le seul dossier régénéré. L’image s’affiche après le délai pédagogique de 1500 ms dans les frames. Les chiffres du tableau restent ceux des archives ; ils ne proviennent pas du clic. Dans les scènes pédagogiques uniquement, l’alternative textuelle est attribuée une fois l’image chargée, de façon identique dans A/B, pour éviter qu’un texte de remplacement ne réserve un espace avant l’image. Les documents de laboratoire conservent src et alt dans leur HTML initial.

## 3. Lancer le serveur expérimental seul

```sh
npm run lab
```

Documents : `http://127.0.0.1:4182/image-layout-stability-demo/experiments/a.html` et `b.html`. Leur image est dans le HTML initial. Seule la réponse de `assets/edikka-image.jpg` attend 2000 ms. `Cache-Control: no-store` et `/__experiment` rendent la condition vérifiable. Une visite manuelle n’est pas automatiquement archivée comme un essai du protocole.

## 4. Exécuter une nouvelle série de mesures

Relire [PROTOCOL.md](PROTOCOL.md), écrit avant la série initiale. Fermer le serveur du port 4182 si la commande doit le démarrer elle-même.

```sh
npm run measure
# Ou réutiliser le serveur de laboratoire déjà démarré :
npm run measure -- --base=http://127.0.0.1:4182/image-layout-stability-demo/
# Sans Chrome installé, utiliser le Chromium installé plus haut :
BROWSER_CHANNEL=bundled npm run measure
```

Sous PowerShell, définir `$env:BROWSER_CHANNEL='bundled'` avant `npm run measure`. Ce navigateur différent produit une **nouvelle série**, il ne remplace pas la série Chrome initiale. La version effective figure dans chaque relevé. Aucun essai n’est lancé par clic dans le document mesuré.

La commande conserve chaque essai, même invalide/échoué, les captures et l’archive des fichiers influents. Les noms contiennent un identifiant daté exclusif (`wx` interdit l’écrasement). En cas d’erreur de navigateur ou de serveur, le manifeste de série conserve l’échec ; ne pas annoncer de mesure absente. Les conditions exactes sont 390×844 et 1440×900 à DPR 1, trois répétitions par variante, ordre A1 B1 B2 A2 A3 B3 par viewport, contexte neuf, cache désactivé, fenêtre [0,6000 ms], page visible sans input/scroll/resize. La collecte prend environ deux minutes selon la machine.

Les `*-run.json` exposent `observation.cls`, `markerDisplacement`, les entrées brutes et leurs sources, les instantanés géométriques, `metricReports`, `visibility`, `interruptions`, l’image et sa réponse, la validité et les erreurs. L’archive `*-sources.zip` permet de retrouver exactement les fichiers de l’expérience. Ne pas modifier les JSON historiques pour suivre une nouvelle version du code.

## 5. Générer les résultats et contrôler

```sh
npm run results
npm run verify
npm test
npm run test:browser
BROWSER=firefox npm run test:browser
```

Le serveur de présentation doit tourner pour les tests navigateur. `DEMO_URL` peut pointer vers une publication GitHub Pages pour la recette de publication, **pas pour substituer une mesure à la série locale retardée**. `TEST_OUTPUT` permet d’archiver cette recette à part. Les tests ne constituent pas un audit complet d’accessibilité.

## 6. Préparer et publier

```sh
npm run prepare:publish
```

La commande vérifie les octets, reconstruit le site, puis teste les données et les références. Le workflow `.github/workflows/pages.yml` construit et publie uniquement `dist/` après une modification de `main`. Il ne relance jamais les mesures et ne publie ni le dossier privé Edikka ni `node_modules/`. Configurer Pages sur **GitHub Actions**. Vérifier l’URL réellement servie, le lancement/rejeu, les ressources, les scènes, le code brut et les observations après chaque livraison.

## English

1. Install **Node 22+** and run `npm ci`. The original series used installed Chrome. `npx playwright install chromium firefox` provides alternative/test browsers.
2. `npm run build` generates the static presentation, then `npm start` serves it at port 4181. No new measurements run.
3. `npm run lab` starts the separate port-4182 server with a 2000 ms delayed image response. The image source is in the initial experimental HTML.
4. `npm run measure` starts that server and collects a new dated 12-trial series. To reuse a running lab, pass `-- --base=http://127.0.0.1:4182/image-layout-stability-demo/`. To use Playwright Chromium, prefix `BROWSER_CHANNEL=bundled` (PowerShell: set `$env:BROWSER_CHANNEL='bundled'`). Browser changes are new conditions, not replacements for old observations.
5. `npm run results` generates HTML from archives; `npm run verify`, `npm test` and `npm run test:browser` validate it. Run `BROWSER=firefox npm run test:browser` for the second browser. `DEMO_URL` and `TEST_OUTPUT` distinguish live-publication checks from local laboratory trials.
6. `npm run prepare:publish` prepares the static artifact. GitHub Pages uses GitHub Actions and publishes `dist/` without rerunning any experiment.

Read the English protocol summary in PROTOCOL.md. The window is 0–6000 ms since navigation, not the entire visit. Unsupported metrics remain null. Preserve all unexpected outcomes and failures. Prior results and their source snapshots are immutable; every new collection uses exclusive dated filenames. No field or real-phone measurements are implied.
