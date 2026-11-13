# TP3 — Réponses

**Binôme :** NOM Prénom — NOM Prénom

> Répondez sous chaque question. Les mesures (durées, pourcentages) sont celles de **votre** machine.

## Partie 1 — Pyramide des tests

**Q1.** Pour chacun des dossiers `tests/unit`, `tests/component`, `tests/integration` et `tests/e2e`, indiquez : le niveau de la pyramide, l'outil utilisé, et si la stack Docker doit tourner pour l'exécuter.
1:
Dossier	  |  Niveau	 |      Outil	 | StackDocker 
tests/unit|	Testunitaire|	Vitest	|Non
tests/component|	Test de composant|	Vitest + Testing Library + jsdom|	Non
tests/integration	|Test d'intégration	|Vitest	|Oui, Strapi et PostgreSQL
tests/e2e	|Test End-to-End|	Playwright (Chromium)	|Oui
**Q2.** Pourquoi la pyramide des tests est-elle large à la base et étroite au sommet ? Donnez au moins deux critères.
La pyramide des tests est large à la base et étroite au sommet pour plusieurs raisons :
1. Le coût et la durée d'exécution : les tests unitaires sont rapides et peu coûteux, tandis que les tests E2E sont plus lents et nécessitent davantage de ressources.
2. La complexité : les tests unitaires vérifient une fonction isolée, alors que les tests E2E nécessitent plusieurs composants, services et parfois une base de données.
3. La facilité de maintenance : les tests unitaires sont généralement plus simples à maintenir et à déboguer que les tests E2E.
4. Le nombre de tests : on privilégie un grand nombre de tests unitaires, moins de tests d'intégration et encore moins de tests E2E.

## Partie 2 — Tests unitaires

**Q3.** Combien de tests sont exécutés et combien échouent ? Recopiez le message d'échec et expliquez ce que signifient `Expected` et `Received`.
Sur les 17 tests exécutés, 16 passent et 1 échoue.
Le test en échec est formatDuration > affiche une heure pleine sans minutes.
Message d'erreur :
AssertionError: expected '1 h 0' to be '1 h'
Expected: "1 h"jh
Received: "1 h 0"

**Q4.** Quelle était la cause du bug dans `formatDuration` ? Expliquez pourquoi la correction fournie le résout.
La fonction formatDuration ajoutait systématiquement les minutes après les heures, même lorsque le nombre de minutes restantes était égal à zéro.
La correction utilise une condition :
return rest === 0 ? `${hours} h` : `${hours} h ${rest}`;
**Q5.** Avant d'ajouter des tests : relevez la couverture (lignes, branches, fonctions) de `lib/cours.ts`. D'après le rapport HTML, quelles lignes ou branches ne sont pas couvertes ?
D'après le premier rapport de couverture :
Type de couverture	Pourcentage
Lignes	93,33 %
Branches	75 %
Fonctions	85,71 %
La ligne non couverte était la ligne 48 de lib/cours.ts.
Cela signifie que certains chemins d'exécution de la fonction n'étaient pas testés.
**Q6.** Après l'ajout des deux tests fournis : quelle est la nouvelle couverture de `lib/cours.ts` ? Quelles lignes ou branches ces tests exécutent-ils qui ne l'étaient pas ? Reste-t-il quelque chose de non couvert, et pourquoi ?
Les deux nouveaux tests ajoutés à filterCours vérifient :
1. Le comportement lorsque la liste des cours est vide.
2. La combinaison de la recherche par mot-clé et du filtrage par niveau.
Ils permettent d'exécuter des chemins supplémentaires de la fonction filterCours, notamment le cas d'une liste vide et celui où plusieurs critères de filtrage sont appliqués simultanément.
**Q7.** Quelle différence entre couverture de lignes et couverture de branches ? Un code couvert à 100 % est-il forcément sans bug ? Justifiez avec un exemple tiré de ce TP.
- Couverture de lignes : mesure le pourcentage de lignes de code exécutées pendant les tests.
- Couverture de branches : mesure le pourcentage de chemins possibles des conditions (if, else, opérateurs conditionnels, etc.) qui ont été exécutés.
Un code couvert à 100 % n'est pas forcément sans bug.
## Partie 3 — Tests de composant

**Q8.** Que vérifie `CoursCard.test.tsx` qu'un test unitaire de `formatDuration` ne vérifie pas ? Quel outil simule le navigateur, et dans quel fichier est-il configuré ?
Un test unitaire de formatDuration vérifie uniquement le résultat du calcul et du formatage d'une durée.

En revanche, CoursCard.test.tsx vérifie le comportement du composant React dans son ensemble :
- L'affichage du titre du cours sous forme de lien.
- La bonne destination du lien.
- L'affichage de la durée formatée.
- L'affichage du niveau du cours.
- L'affichage des mots-clés.
- L'absence de la liste des mots-clés lorsqu'elle est vide.
L'environnement qui simule le navigateur est jsdom. Il permet d'exécuter les tests des composants React sans ouvrir un véritable navigateur.
**Q9.** Pourquoi les tests recherchent-ils les éléments par rôle ou par texte (`getByRole`, `getByText`) plutôt que par classe CSS ?
Les tests utilisent getByRole et getByText parce qu'ils vérifient les éléments selon leur rôle et leur contenu, comme le ferait un utilisateur.

Cela présente plusieurs avantages :
- Les tests sont plus proches de l'utilisation réelle de l'application.
- Ils sont moins dépendants de la structure et du style CSS.
- Ils restent plus stables si les classes CSS changent.
- Ils favorisent l'accessibilité de l'application.
**Q10.** Pourquoi n'y a-t-il pas de test de composant pour `app/page.tsx` ? Quel niveau de test le couvre ?
app/page.tsx représente la page principale de l'application. Elle assemble plusieurs composants et participe au fonctionnement global de l'application.

Il n'y a pas de test de composant spécifique pour cette page parce que son comportement global est vérifié au niveau des tests E2E, qui simulent les actions d'un utilisateur dans l'application.

Ces tests utilisent Playwright pour vérifier le fonctionnement de l'application dans un véritable navigateur, notamment l'affichage des pages et les interactions utilisateur.
## Partie 4 — Tests d'intégration

**Q11.** Que vérifient les tests d'intégration qui n'est vérifié par aucun test unitaire ou de composant ?
Les tests d'intégration vérifient la communication réelle entre le frontend Next.js et l'API Strapi. Ils s'assurent que les données sont correctement récupérées depuis le serveur, notamment les 6 cours, leur tri et la recherche par identifiant.
Les tests unitaires et de composant ne vérifient pas cette communication réelle avec Strapi.
**Q12.** Strapi arrêté : quel résultat et quel message obtenez-vous ? Pourquoi les tests unitaires passent-ils toujours ? À quoi sert `--no-deps` ?
Lorsque Strapi est arrêté, les 4 tests d'intégration échouent avec les messages :
TypeError: fetch failed
Error: getaddrinfo ENOTFOUND strapi
Les tests unitaires et de composant passent toujours (19 tests réussis), car ils ne dépendent pas du serveur Strapi.
L'option --no-deps empêche Docker Compose de redémarrer automatiquement les services dépendants, notamment Strapi. Elle permet donc de tester le comportement lorsque Strapi est arrêté.
**Q13.** Un test d'intégration en échec signale-t-il forcément un bug dans le code ? Citez deux autres causes possibles.
Non, un test d'intégration en échec ne signifie pas forcément qu'il existe un bug dans le code.
Deux autres causes possibles sont :
Le serveur Strapi est arrêté ou inaccessible, ce qui empêche les appels API.
La base de données PostgreSQL est indisponible, ce qui empêche Strapi de récupérer les données nécessaires.
## Partie 5 — Tests de bout en bout

**Q14.** Quel scénario échoue ? Que montrent la capture d'écran et la trace ? Quel lien avec la question Q3 ? Pourquoi échoue-t-il alors que vous avez déjà corrigé le bug en partie 2 ?
Le scénario qui échoue est « La fiche du cours d'introduction affiche sa durée ».
La capture d'écran et la trace montrent que l'application affiche 1 h 0 au lieu de 1 h. Playwright attend le texte 1 h, mais ne le trouve pas après 5 secondes.
Ce problème correspond au bug de la fonction formatDuration identifié en Q3.
Le test échoue malgré la correction effectuée en Partie 2, car le conteneur frontend utilise encore l'ancienne image Docker. Il faut reconstruire le frontend avec docker compose up -d --build frontend.
**Q15.** Le même bug est détecté par un test unitaire et par un test E2E. Lequel permet de trouver la cause le plus vite ? Lequel prouve que l'utilisateur est touché ?
Le test unitaire permet de trouver la cause plus rapidement, car il teste directement la fonction formatDuration.
Le test E2E prouve que l'utilisateur est touché, car il vérifie le comportement réel de l'application dans un navigateur.
**Q16.** Complétez le tableau avec vos mesures :

| Niveau | Nombre de tests | Durée d'exécution | Besoin de la stack ? |
|---|---|---|---|
| Unitaire + composant |19 |1.27 |non |
| Intégration |4 | 417ms| 

Oui, Strapi et PostgreSQL|
| E2E |4 |9.5s |

Oui, stack complète |

**Q17.** Qu'est-ce qu'un test « flaky » (instable) ? Quels mécanismes de Playwright utilisés ici (attente automatique, assertions `expect(...).toHaveCount`, etc.) réduisent ce risque ?
Un test flaky est un test qui réussit parfois et échoue parfois sans modification du code, par exemple à cause de problèmes de synchronisation ou de chargement.

Playwright réduit ce risque grâce à :

L'attente automatique : il attend que les éléments soient prêts avant d'interagir avec eux.
Les assertions expect() : toHaveCount(), toBeVisible() et toHaveURL() réessaient automatiquement les vérifications jusqu'à leur réussite ou l'expiration du délai.
Les locators : getByRole() et getByText() permettent de sélectionner les éléments de manière plus fiable.
Les timeouts : ils définissent une durée maximale d'attente et évitent de dépendre de pauses fixes.
## Partie 6 — SonarQube

**Q18.** Collez votre `sonar-project.properties` complété et expliquez chaque valeur ajoutée. Pourquoi exclut-on `app/**` et `lib/api.ts` du calcul de couverture ?

**Q19.** Analyse avec le Quality Gate TP3 : statut du gate et conditions en échec ; nombre de bugs, vulnérabilités, security hotspots et code smells ; couverture ; taux de duplication.

**Q20.** Expliquez la différence entre bug, vulnérabilité, security hotspot et code smell, avec un exemple tiré de votre rapport quand il en existe.

**Q21.** Ouvrez la vulnérabilité : quelle règle est en cause et quel est le risque ? Expliquez la correction fournie : d'où vient maintenant le mot de passe, et par quel chemin arrive-t-il jusqu'au code ? Pourquoi un secret dans le code est-il dangereux, même dans un dépôt privé ?

**Q22.** Quels code smells la correction fournie de `CoursList.tsx` fait-elle disparaître ? Pour chacun : règle, sévérité, explication, et ce qui, dans le nouveau code, règle le problème.

**Q23.** Où se trouve le code dupliqué ? Comment le supprimer ? (Ne le faites pas, décrivez seulement.)

**Q24.** Qu'est-ce que la dette technique dans SonarQube et comment est-elle exprimée ? Valeur avant et après vos corrections.

**Q25.** Que signifie « New Code » dans SonarQube ? Quelles conditions du gate TP3 portent sur le nouveau code ? Pourquoi SonarQube recommande-t-il de juger le nouveau code plutôt que le code global ?

**Q26.** Avec `sonar.qualitygate.wait=true`, que fait le scanner quand le Quality Gate est rouge ? Pourquoi est-ce utile dans un pipeline CI ?

## Partie 7 — Synthèse

**Q27.** Proposez l'ordre des étapes d'un pipeline CI pour ce projet (build, tests unitaires, intégration, E2E, analyse Sonar…) et justifiez-le. Quelles étapes doivent bloquer le merge d'une pull request ?
