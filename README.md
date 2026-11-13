# Permanences du week-end

> Répartissez équitablement les permanences de week-end d'une équipe, sans qu'aucune donnée ne quitte votre dossier partagé.

Une page web autonome dans laquelle chaque agent coche ses week-ends d'indisponibilité, et qui propose ensuite un planning où la charge est répartie le plus également possible. Elle s'ouvre depuis un dossier partagé, sans installation, sans serveur et sans connexion à Internet.

![HTML](https://img.shields.io/badge/HTML%20%2B%20JavaScript-un%20seul%20fichier-3776AB)
![Dépendances](https://img.shields.io/badge/d%C3%A9pendances-aucune-16a34a)
![Réseau](https://img.shields.io/badge/connexion%20r%C3%A9seau-aucune-16a34a)
![Licence](https://img.shields.io/badge/licence-GPL%20v3-6b7280)

---

## Le problème

Dans une équipe qui assure des permanences de week-end, le planning se construit le plus souvent à la main, dans un tableur : on collecte les indisponibilités de chacun, on place les noms, puis on recompte pour vérifier que personne n'en fait plus que les autres. Les week-ends prolongés par un jour férié compliquent le compte, et chaque changement de dernière minute oblige à tout revérifier.

Cette page collecte les indisponibilités, calcule une répartition équilibrée et signale les anomalies, à partir d'un simple fichier posé dans le dossier partagé de l'équipe.

## Fonctionnalités

- **Saisie des indisponibilités par chaque agent** — chacun choisit son nom et coche les week-ends où il n'est pas disponible. Les saisies des collègues apparaissent sans action, au plus tard après 30 secondes.
- **Proposition automatique de planning** — tous les postes sont pourvus en réduisant l'écart de charge entre agents. Sur le premier semestre 2027 avec 6 agents, l'écart obtenu entre le plus et le moins sollicité est de 2 jours, soit un week-end.
- **Ajustement sans tout refaire** — quand un agent devient indisponible ou qu'un nouvel agent arrive, le mode « Ajuster » ne modifie que les week-ends nécessaires.
- **Jours fériés pris en compte** — les 11 jours fériés nationaux sont calculés pour chaque année, Pâques compris. Un week-end prolongé compte 3 jours au lieu de 2.
- **Arrivée en cours de période** — un agent peut avoir une date d'arrivée : il n'est jamais affecté avant, et sa charge attendue est réduite d'autant.
- **Alertes** — poste non pourvu, agent affecté alors qu'il est indisponible, agent affecté deux fois, deux week-ends d'affilée.
- **Travail à plusieurs en même temps** — deux agents peuvent enregistrer au même instant depuis deux postes : aucune saisie n'écrase l'autre. Voir [Lecture et écriture simultanées](#lecture-et-écriture-simultanées).
- **Période de plusieurs années** — jusqu'à 20 ans, affichés une année à la fois.
- **Impression et PDF** — planning, vue d'ensemble, et fiche individuelle d'un agent sur une page A4.
- **Journal des modifications** — un fichier texte lisible dans le Bloc-notes, une ligne par action.

## Sécurité et confidentialité des données

Le planning contient des noms et des indisponibilités : ce sont des données personnelles. L'outil est conçu pour qu'elles restent là où vous les avez mises.

### Ce que la page garantit

| Garantie | Comment elle est obtenue |
|---|---|
| **Aucune donnée n'est envoyée sur Internet** | La page interdit elle-même toute connexion réseau (`connect-src 'none'` dans sa politique `Content-Security-Policy`). Même un code défectueux ne pourrait pas transmettre de données : le navigateur bloque la requête. |
| **Aucun code extérieur n'est chargé** | Ni bibliothèque, ni police, ni image, ni outil de mesure d'audience (`default-src 'none'`). Tout ce qui s'exécute est écrit dans le fichier, et peut être relu. |
| **Aucun serveur, aucun compte, aucun mot de passe** | Il n'y a rien à pirater en ligne : les données sont deux fichiers dans votre dossier partagé. |
| **Accès limité à un seul dossier** | La page ne lit et n'écrit que dans le dossier que l'utilisateur a choisi et autorisé dans Edge. Cette autorisation se retire à tout moment dans le navigateur. |
| **Rien de sensible sur le poste** | Le navigateur ne mémorise que le lien vers le dossier, le dernier nom choisi, le thème et l'état de l'interrupteur Administration. Aucune donnée de planning n'y est stockée. |
| **Un journal sans auteur nominatif** | Chaque ligne indique le rôle (`Agent` ou `Administration`), jamais le nom de la personne qui a agi ni son poste. |
| **Aucune saisie perdue** | Avant d'écrire, la page relit le fichier, applique la seule modification demandée, vérifie que personne n'a écrit entre-temps, puis contrôle après 0,8 s, 2,5 s et 6 s que la modification est toujours là. Sinon elle la réapplique. Tant que ce contrôle n'est pas terminé, Edge demande confirmation avant de fermer la page. |
| **Aucun fichier écrasé** | Un fichier de données existant n'est jamais recréé. S'il est illisible, la page refuse de l'ouvrir et ne modifie rien. |
| **Texte saisi neutralisé** | Les noms et le titre sont échappés avant affichage : un nom contenant du code ne peut pas s'exécuter. |

### Ce que la page ne fait pas

Ces limites sont à connaître avant la mise en place.

- **L'interrupteur « Administration » n'est pas une protection.** Il n'a pas de mot de passe et sert seulement à alléger l'écran des agents. Toute personne qui ouvre la page peut l'activer.
- **Il n'y a pas de vérification d'identité.** Chaque agent se désigne lui-même dans une liste : rien n'empêche de saisir une indisponibilité pour un collègue, et le journal n'indique pas qui l'a fait.
- **Le contrôle d'accès repose entièrement sur les droits Windows du dossier partagé.** Qui peut écrire dans le dossier peut modifier le planning, y compris en ouvrant le fichier JSON à la main.
- **Les données ne sont pas chiffrées.** `permanences-donnees.json` et `permanences.log` sont lisibles par toute personne ayant accès au dossier. Le journal contient les noms des agents concernés par chaque action.
- **Si le dossier est synchronisé vers un service en ligne** (OneDrive, SharePoint…), ce service copie les fichiers dans le cloud. Ce n'est pas la page qui les y envoie, mais le résultat est le même : à décider en connaissance de cause.
- **Une sauvegarde téléchargée sort du dossier protégé.** Elle arrive dans le dossier Téléchargements du poste : à ranger ou à supprimer ensuite.

### Recommandations

1. Réserver le dossier partagé aux membres de l'équipe, par les droits Windows.
2. Ne pas placer le dossier dans un espace synchronisé en ligne si les données ne doivent pas quitter le réseau interne.
3. Avant un geste sans retour (retirer un agent, effacer le planning, changer la période), télécharger une copie des données : Réglages → « Télécharger une sauvegarde des données ». L'enregistrement en temps réel conserve chaque saisie, y compris une erreur ; seule une copie permet de revenir en arrière.

### Auditer le code

Tout le code tient dans `permanences.html`, lisible sans outil. Il est commenté pour une relecture par un service technique :

- un **guide de lecture** en tête de fichier : structure, flux de données, stockage, points de contrôle, limites assumées ;
- la **politique de sécurité** expliquée directive par directive ;
- chaque fonction décrite : rôle, paramètres, résultat, et points d'attention quand il y en a.

| Question d'audit | Où regarder |
|---|---|
| La page peut-elle émettre vers le réseau ? | Balise `Content-Security-Policy`, en tête de fichier |
| Une donnée saisie peut-elle exécuter du code ? | `esc()` et la règle énoncée en tête du script Interface |
| Que vaut un fichier de données modifié à la main ? | `Core.normalize()` |
| Quels fichiers sont lus et écrits ? | `fileIo()`, `openFolder()` |
| Deux écritures simultanées peuvent-elles se perdre ? | `Core.Store` (dont `busy()` et `onIdle`) |
| Quand la page avertit-elle avant la fermeture ? | Écouteur `beforeunload`, en fin de fichier |
| Que contient le journal ? | `Core.logLine()`, `execute()` |

## Comment un week-end est compté

On compte en **jours de permanence**. La règle est fixe, sans réglage :

| Week-end | Jours comptés |
|---|---|
| Ordinaire (samedi + dimanche) | **2** |
| Prolongé par un férié le **vendredi ou le lundi** | **3** |
| Férié qui tombe le **samedi ou le dimanche** | **2** — aucun jour en plus |

Exemples réels en 2027 :

| Week-end | Jour férié | Jours comptés |
|---|---|---|
| 2 – 3 janvier | Jour de l'an, vendredi 1er | 3 |
| 27 – 28 mars | Lundi de Pâques, le 29 | 3 |
| 1er – 2 mai | Fête du Travail, samedi 1er | 2 |
| 15 – 16 mai | Lundi de Pentecôte, le 17 | 3 |
| 30 – 31 octobre | Toussaint, lundi 1er novembre | 3 |
| 25 – 26 décembre | Noël, samedi 25 | 2 |

Sur l'année 2027 : 52 week-ends, dont 4 prolongés, soit 48 × 2 + 4 × 3 = **108 jours** à répartir.

La **charge** d'un agent est le total des jours de ses week-ends de permanence. Un agent qui assure 3 week-ends ordinaires et 1 week-end prolongé a une charge de 3 × 2 + 3 = **9 jours**.

Un week-end est désigné par la date de son samedi, et appartient à l'année de ce samedi.

### La charge attendue de chaque agent

Par défaut, tous les agents doivent la même charge. Seule la **date d'arrivée**, saisie dans l'onglet Agents, la module : un agent n'est jamais affecté avant cette date, et seuls les week-ends postérieurs comptent pour lui.

```
poids d'un agent  = jours des week-ends où il est présent
charge attendue   = jours effectivement répartis × poids de l'agent ÷ somme des poids de tous les agents
```

Si tous les agents sont présents depuis le début, tous les poids sont égaux : la charge attendue est simplement la moyenne.

Dans le tableau « Charge par agent », la colonne **Attendu** donne cette charge, **Effectué** les jours réellement affectés, et **Écart** la différence entre les deux.

## Comment la répartition est calculée

### 1. Les règles

| Règle | Niveau |
|---|---|
| Un agent indisponible, ou pas encore arrivé, n'est jamais affecté | **Interdit** : le calcul ne l'envisage pas |
| Un week-end passé ou verrouillé n'est jamais modifié | **Interdit** |
| Tous les postes doivent être pourvus | Priorité 1 |
| Pas deux week-ends d'affilée pour le même agent (option) | Priorité 2 |
| La charge de chacun doit être la plus proche possible de sa charge attendue | Priorité 3 |
| En mode « Ajuster », changer le moins possible | Priorité 4 |

Les week-ends passés ne sont pas modifiés, mais ils **comptent dans la charge** : un agent qui a déjà beaucoup donné en début de période en reçoit moins ensuite.

### 2. La note d'un planning

Chaque planning candidat reçoit une note. **Plus elle est basse, meilleur il est.**

```
note = 1 000 000 × nombre de postes non pourvus
     +     1 000 × nombre de week-ends consécutifs       (si l'option est activée)
     +        10 × inégalité de charge sur toute la période
     +         3 × inégalité de charge dans chaque année    (période de plusieurs années)
     +         1 × écart entre l'agent le plus au-dessus et le plus en dessous de sa part
     +         2 × nombre d'affectations modifiées          (mode « Ajuster » seulement)
```

Les coefficients sont si éloignés qu'ils forment une hiérarchie : un seul poste vide coûte plus cher que n'importe quel déséquilibre, et un seul enchaînement de deux week-ends coûte plus cher que n'importe quel gain d'équité.

L'**inégalité de charge** est la somme des carrés des écarts entre la charge de chaque agent et sa charge attendue. Pour cette note, les charges sont exprimées en week-ends (2 jours = 1, 3 jours = 1,5). Avec trois agents de même part chargés de 4, 5 et 6, la part attendue est 5 et l'inégalité vaut (−1)² + 0² + 1² = 2. S'ils sont chargés de 5, 5 et 5, elle vaut 0. Le carré pénalise fortement les gros écarts : mieux vaut deux agents à ±1 qu'un seul à +2.

### 3. La recherche du meilleur planning

Le calcul construit plusieurs plannings et garde celui qui a la note la plus basse.

**Étape A — Construire un premier planning.** Les week-ends sont traités du plus contraint au moins contraint : celui où le moins d'agents sont disponibles passe en premier, car c'est lui qui laisse le moins de choix. Chaque poste reçoit l'agent disponible le moins chargé à ce moment-là, au regard de sa part. Une petite part de hasard départage les agents à égalité, pour que chaque essai explore une piste différente.

**Étape B — L'améliorer.** Deux gestes sont essayés partout, et gardés seulement s'ils font baisser la note :

- *remplacement* : un agent affecté cède son week-end à un autre agent disponible ;
- *échange* : deux agents échangent leurs week-ends.

On recommence jusqu'à ce que plus aucun geste n'améliore la note.

**Étape C — Recommencer.** Les étapes A et B sont répétées au moins 6 fois, et jusqu'à 80 fois si le temps le permet (environ 1,2 seconde). Le meilleur planning est affiché.

### 4. Deux exemples chiffrés

**Agents présents toute la période.** Période du 1er janvier au 30 juin 2027, 6 agents, 1 agent par week-end, trois indisponibilités saisies.

- 26 week-ends, dont 3 prolongés : total à répartir = 23 × 2 + 3 × 3 = **55 jours**.
- Charge attendue de chacun : 55 ÷ 6 = **9,17 jours**.
- Résultat du calcul : trois agents à **9 jours** (3 week-ends ordinaires + 1 prolongé), deux agents à **10 jours** et un agent à **8 jours**.
- Écart entre le plus et le moins chargé : **2 jours**. Aucune alerte.

Il est impossible de faire mieux : un total impair exige un week-end prolongé, et il n'y en a que 3. Les trois autres agents se partagent 14 week-ends ordinaires, soit 28 jours : 10, 10 et 8.

**Avec une arrivée en cours de période.** Même période, même équipe, sans indisponibilité, mais Bruno arrive le 1er avril.

| Agent | Charge attendue | Résultat obtenu |
|---|---|---|
| Bruno (arrivé le 1er avril) | 4,92 jours | 5 jours |
| Chacun des cinq autres | 10,02 jours | 10 jours |

Bruno n'est affecté à aucun des 13 week-ends d'avant avril. L'écart, mesuré par rapport aux charges attendues, est de 0,1 jour.

### 5. Deux façons de lancer le calcul

- **Nouvelle proposition complète** — tous les week-ends à venir non verrouillés sont recalculés.
- **Ajuster le planning** — le calcul part du planning enregistré et chaque affectation modifiée alourdit la note. Seul le nécessaire change.

Le résultat est d'abord un **aperçu**, visible sur le seul poste qui l'a calculé. Il se corrige à la main (un week-end corrigé est verrouillé) et n'est partagé qu'après « Enregistrer le planning ».

### 6. Ce qu'il faut savoir sur le résultat

- **Le hasard intervient.** Deux lancements peuvent donner deux plannings différents, de qualité équivalente.
- **Le calcul cherche un très bon planning, pas le meilleur absolu garanti.** Sur une petite équipe et une période courte, il l'atteint en pratique.
- **L'équilibre porte sur la période entière et sur chaque année.** L'affichage par année peut montrer un écart que le cumul compense.
- **Le prorata ne connaît que la date d'arrivée.** Un temps partiel, un départ en cours de période ou une longue absence se traitent par des indisponibilités, qui ne réduisent pas la charge attendue.

## Technologies

| Outil | Rôle |
|---|---|
| HTML, CSS et JavaScript | La page entière, dans un seul fichier, sans bibliothèque ni étape de compilation |
| [File System Access](https://developer.mozilla.org/fr/docs/Web/API/File_System_API) | Lecture et écriture dans le dossier partagé, après accord de l'utilisateur |
| [Content-Security-Policy](https://developer.mozilla.org/fr/docs/Web/HTTP/Guides/CSP) | Interdiction de toute connexion réseau et de toute ressource externe |
| IndexedDB et localStorage | Mémorisation du dossier choisi et des préférences d'affichage du poste |

## Installation

Prérequis : Microsoft Edge sous Windows, et un dossier partagé accessible en écriture par l'équipe.

1. Télécharger `permanences.html` et le copier dans le dossier partagé.
2. L'ouvrir avec Edge, cliquer sur « Choisir le dossier… » et sélectionner ce même dossier.
3. Accepter la création de `permanences-donnees.json`. Cette étape n'a lieu qu'une fois.
4. Ajouter les agents, régler la période, puis proposer un planning.

Pour récupérer le dépôt complet :

```bash
git clone https://github.com/Incapas/duty-roster-planner.git
```

## Utilisation

Chaque agent ouvre `permanences.html` depuis le dossier partagé, choisit son nom dans « Mes indisponibilités » et coche ses week-ends. L'administration active l'interrupteur en haut à droite pour accéder aux onglets Agents et Réglages et aux actions du planning.

Pièges connus :

- **Message « Ouvrez cette page avec Microsoft Edge »** : le navigateur utilisé ne sait pas écrire dans un dossier. Clic droit sur le fichier → « Ouvrir avec » → Edge.
- **Écran « Autoriser l'accès » à chaque ouverture** : choisir « Autoriser à chaque visite » dans la fenêtre d'Edge.
- **La page, les données et le journal doivent être dans le même dossier.** La page prévient si ce n'est pas le cas.
- **« Enregistrement retardé… »** : le partage est momentanément inaccessible. La page réessaie seule.
- **« Le fichier … est vide ou illisible »** : le fichier de données est abîmé. Rien n'a été modifié ; restaurer une sauvegarde.
- **Le journal n'est pas mis à jour** : vérifier les droits d'écriture sur `permanences.log`.
- **Edge demande « Quitter le site ? » à la fermeture** : une saisie est en cours d'enregistrement ou de vérification. Choisir « Annuler », attendre « Enregistré et vérifié à … », puis fermer.

## Lecture et écriture simultanées

Plusieurs personnes peuvent ouvrir la page en même temps, chacune depuis son poste. Il n'y a ni serveur ni verrou : chaque poste lit et écrit directement `permanences-donnees.json` dans le dossier partagé.

### Les saisies

Chaque clic est enregistré aussitôt, et seule la modification demandée est écrite, par exemple « Alice est indisponible le 9 janvier ». La page :

1. relit le fichier ;
2. y applique cette seule modification ;
3. relit le fichier juste avant d'écrire. S'il a changé, un collègue vient d'écrire : elle recommence après une courte attente aléatoire, jusqu'à 25 fois ;
4. écrit le fichier ;
5. contrôle après 0,8 s, 2,5 s et 6 s que la modification est toujours là. Sinon, elle la réapplique.

| Situation | Résultat |
|---|---|
| Deux agents cochent leurs cases au même moment | Les deux saisies sont conservées |
| Deux personnes modifient la **même** case, le même nom, la même date d'arrivée ou le même réglage | La dernière saisie l'emporte |
| Deux administrateurs enregistrent chacun un planning | Le dernier enregistré remplace l'autre en entier |
| Un agent coche une case pendant que l'administration le supprime | La saisie est ignorée, l'agent n'est pas recréé |
| Deux postes ajoutent le même nom | L'agent n'est créé qu'une fois |

Le journal `permanences.log` est protégé de la même façon : les lignes écrites au même moment par deux postes sont toutes conservées.

### Quand fermer la page

La ligne d'état, en bas de l'écran, indique où en est la dernière saisie :

| Message | Signification |
|---|---|
| « Enregistrement… » | L'écriture est en cours |
| « Enregistré, vérification en cours… » | La saisie est écrite ; les contrôles de l'étape 5 ne sont pas terminés |
| « Saisie simultanée fusionnée, vérification en cours… » | Un collègue a écrit au même instant ; la saisie a été réappliquée et elle est de nouveau contrôlée |
| « Enregistré et vérifié à hh:mm » | Tout est écrit et contrôlé : la page peut être fermée |
| « Une saisie n'a pas pu être enregistrée… » | Le fichier est resté inaccessible malgré les nouveaux essais : recharger la page et vérifier ses dernières saisies |

Il faut compter environ 7 secondes après le dernier clic. Si l'on ferme la page avant, Edge affiche « Quitter le site ? » ; le texte de ce message est celui d'Edge et ne peut pas être modifié. Fermer quand même expose à deux risques :

- avant « Enregistré… », la saisie peut ne jamais être écrite ;
- pendant la vérification, la saisie est écrite, mais elle se perd si un collègue a écrit au même instant à partir d'une version qui ne la contenait pas : le contrôle qui l'aurait réappliquée n'a pas lieu.

Ces contrôles ne peuvent pas être terminés pendant la fermeture : le navigateur arrête les écritures de fichier dès que la page se ferme.

### La visibilité des mises à jour

- **Sur son propre poste**, une saisie s'affiche immédiatement.
- **Sur les autres postes**, elle apparaît à la relecture suivante du fichier : toutes les 30 secondes, au retour sur la fenêtre ou sur l'onglet, ou au clic sur « Recharger ». Un onglet laissé en arrière-plan ne relit pas toutes les 30 secondes, mais il relit dès qu'on y revient.
- **Un aperçu de planning** n'est visible que sur le poste qui l'a calculé, tant qu'il n'est pas enregistré. Une indisponibilité saisie entre-temps par un agent arrive à la relecture suivante et apparaît comme alerte sur l'aperçu ; l'enregistrement demande confirmation si un agent indisponible est affecté.

### En pratique

- Les agents peuvent saisir leurs indisponibilités en même temps sans précaution particulière.
- Un seul administrateur à la fois devrait calculer et enregistrer un planning.
- Fermer la page après « Enregistré et vérifié à … ».

## Tests

Le projet n'a pas de suite de tests automatisés : c'est un choix, pour un outil qui tient dans un seul fichier.

La logique métier (dates, fériés, comptage, répartition, synchronisation) est isolée dans un module `Core` qui ne dépend pas de l'écran. Il s'extrait et s'exécute hors navigateur, par exemple sous macOS :

```bash
sed -n '/\/\*CORE-DEBUT\*\//,/\/\*CORE-FIN\*\//p' permanences.html > core.js
```

```bash
/System/Library/Frameworks/JavaScriptCore.framework/Versions/Current/Helpers/jsc -e "load('core.js'); print(Core.periodWeekends(Core.newData().reglages).length)"
```

La seconde commande affiche `26`, le nombre de week-ends du premier semestre 2027. Les chiffres de ce README (comptage 2027, exemples de répartition) ont été vérifiés de cette façon.

## Structure du projet

```
permanences.html               La page : interface et logique
  script de thème              Applique le thème clair ou sombre avant l'affichage
  Core                         Logique métier pure : dates, fériés, données, synchronisation, répartition
  Interface                    État de l'écran, rendu, clics, accès au dossier, impression
LICENSE                        GNU GPL v3
```

`Core` ne touche jamais à l'écran et l'interface ne contient aucune règle métier : une règle de calcul se modifie dans `Core`, et se vérifie sans navigateur.

Le code est écrit en anglais : variables, fonctions, propriétés, classes CSS et actions des boutons. Les commentaires, les textes affichés et le journal restent en français, tout comme les clés du fichier de données décrites ci-dessous, pour que l'administration puisse le relire dans le Bloc-notes.

### Fichiers de données

La page crée deux fichiers dans le dossier partagé. Ils contiennent des données personnelles et ne doivent pas être versés dans le dépôt.

```
permanences-donnees.json       Réglages, agents, indisponibilités, planning
permanences.log                Journal des modifications, une ligne par action
```

```json
{
  "format": "permanences-weekend",
  "version": 1,
  "reglages": { "titre": "Permanences du week-end", "debut": "2027-01-01", "fin": "2027-06-30",
                "parWeekend": 1, "eviterConsecutifs": true },
  "salaries": [ { "id": "s…", "nom": "Camille Martin", "depuis": "" } ],
  "retires": [],
  "indispos": { "<id agent>": [ "2027-01-09" ] },
  "planning": { "affectations": { "2027-01-02": [ "<id agent>" ] }, "verrous": [], "enregistreLe": null },
  "journal": [],
  "majLe": null
}
```

Un week-end est désigné par la date de son samedi. `retires` liste les agents supprimés, pour qu'une saisie en retard ne les recrée pas. `journal` reste vide dans ce fichier. `depuis` est la date d'arrivée, vide si l'agent est présent depuis le début.

Une ligne du journal :

```
2026-11-12 21:17:45 | Administration | Camille Martin : indisponible le sam. 9 – dim. 10 janv. 2027 [#smwc9yszcg7j4i3]
```

Pour restaurer une sauvegarde, la replacer dans le dossier sous le nom `permanences-donnees.json`.

### Modifier l'outil

| Je veux… | Où intervenir |
|---|---|
| Ajouter un jour férié | `Core.holidays()` |
| Changer ce que compte un week-end | `Core.periodWeekends()` |
| Modifier les priorités de la répartition | `cost()` dans `Core.propose()` |
| Ajouter un réglage | `defaultSettings`, `normalize`, `SETTING_NAMES`, `settingsView`, action `save-settings` |
| Ajouter un type de modification | Un `case` dans `Core.applyOp()`, qui doit rester rejouable sans effet, et sa clé dans `opKey()` |

## Contributeurs

### Initiateur du besoin — _à compléter_

À l'origine de l'idée et des règles métier : permanences de week-end réparties équitablement, prise en compte des indisponibilités et des week-ends prolongés.

### Réalisateur — [@Incapas](https://github.com/Incapas)

Réalisation et mise à disposition de l'outil à partir du besoin exprimé :

- traduction du besoin en fonctionnalités ;
- direction du travail des agents de code, relecture et validation ;
- publication du projet.

### Agents de code

#### Claude Fable 5.2 via Claude Code

Sous la direction du réalisateur :

- rédaction de ce README, dont les chiffres ont été vérifiés par exécution du module `Core` ;
- commentaires d'audit de `permanences.html` ;
- comptage en jours à la place des poids réglables, date d'arrivée des agents ;
- retrait du code d'annulation inutilisé et correction de l'affichage des erreurs.

#### Claude Opus 5.5 via Claude Code

Sous la direction du réalisateur, le reste du développement :

- écriture de `permanences.html` : interface, calcul des dates et des jours fériés, répartition automatique, alertes, impression et journal ;
- synchronisation du fichier partagé (`Core.Store`), avertissement avant la fermeture de la page et suivi de l'enregistrement dans la ligne d'état ;
- section « Lecture et écriture simultanées » de ce README ;
- `.gitignore` et préparation du dépôt pour une première utilisation.
- passage du code en anglais et des déclarations `var` en `let` et `const`.

## Licence

[GNU General Public License, version 3](LICENSE).
