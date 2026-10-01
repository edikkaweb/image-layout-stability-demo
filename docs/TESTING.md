# Vérifications / Verification

Les tests portent sur l’identité de l’intervention, la fidélité des données et les comportements de la présentation. Ils ne prouvent ni conformité générale ni résultat terrain.

- `npm run verify` : empreintes image/web-vitals, différence HTML A/B bornée, références et cohérence des relevés.
- `npm test` : regroupement CLS par fenêtres (écart 1 s, durée 5 s, hadRecentInput), dimensions JPEG réelles, répétitions et archives, HTML généré, liens sous le chemin de projet et sources HTML inchangées.
- `npm run test:browser` : variantes réellement rendues, mêmes dimensions des frames, A déplacé / B stable dans la recette, plusieurs rejeux rapides, FR/EN, 390 et 1440 px, clavier et focus, mouvement réduit, erreur d’image, API indisponible = null, sans JavaScript et axe-core.
- `BROWSER=firefox npm run test:browser` : contrôle du second moteur, sans lui attribuer une métrique non prise en charge.

Les scénarios de recette qui attendent un comportement ne changent pas les résultats du laboratoire. Un essai archivé sans décalage inattendu reste dans les résultats ; il n’est pas remplacé pour faire passer un test. Les captures retenues sont examinées visuellement. Les journaux de recette se trouvent hors `results/` dans `test-results/`, ou à l’emplacement `TEST_OUTPUT` choisi. La preuve de publication GitHub/Edikka est conservée dans le cockpit Edikka, distinctement des relevés publics.

Limites : pas de téléphone réel ni lecteur d’écran testé ; les viewports sont émulés. Une analyse axe sans violation ne certifie pas l’accessibilité. Les vérifications indéterminées et échecs éventuels restent dans les rapports. Aucun audit de formats, compression, LCP, srcset ou lazy loading dans cette version.

English: tests cover experimental identity, session-window CLS semantics, raw-to-HTML fidelity and presentation behaviour. Browser checks include repeated playbacks, image failures, unsupported API handling, no-JavaScript content, keyboard/reduced motion, responsive layouts and a second engine. They do not replace the actual laboratory protocol, field evidence, screen-reader testing or a complete accessibility audit. Test reports are kept separately from immutable measurement results.

Précision de recette : axe est exécuté séparément dans chaque document (parent avec iframes:false, puis chacune des scènes). Une première analyse fusionnant les documents a signalé heading-order et landmark-unique en mélangeant leurs h1/main indépendants ; ce rapport est conservé comme limite de cette analyse combinée, sans changer la structure correcte des documents pour l’effacer.
