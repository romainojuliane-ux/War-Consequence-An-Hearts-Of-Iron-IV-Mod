WAR CONSEQUENCES — VERSION 0.2.1
================================

NOTES DE LA VERSION 0.2.1
-------------------------
- Coup d'État : nouvel événement (war_consequences.10) qui change réellement le régime
  via set_politics, en cas de crise militaire et politique extrême avec stabilité quasi nulle.
  Le coup est testé par le jeu uniquement après validation : vérifie les logs.
- Pertes industrielles : le mod compare désormais le nombre d'usines au début de la guerre
  (wc_factories_base) au nombre actuel. Les seuils (5, 15, 30 usines) pèsent sur l'économie.
- Non modifié : séparatisme, paix séparée réelle, gouvernement en exil réel, pertes
  humaines et matérielles restent non implémentés (voir sections ci-dessous).


CIBLE ET PRÉREQUIS
------------------
Cible de travail : Hearts of Iron IV 1.19.x.
Dernière version déclarée par l'utilisateur : 1.19.0.0.
Le jeu n'est pas installé dans l'environnement de génération : aucun test en partie,
aucun lancement du moteur et aucune validation du parser HOI4 n'ont été réalisés.
Le champ supported_version est donc une indication de cible, pas une preuve de compatibilité.
Si ton jeu est toujours en 1.19.0.0, commence par cette version et teste sur une copie.

INSTALLATION PROPRE
-------------------
1. Ferme HOI4 et le Paradox Launcher.
2. Fais une copie de sauvegarde de ton dossier mod précédent.
3. Supprime l'ancien dossier mod/War_Consequences (ne mélange pas les fichiers 0.1 et 0.2).
4. Copie le dossier War_Consequences dans :
   Documents/Paradox Interactive/Hearts of Iron IV/mod/
   ou le dossier Documents redirigé vers OneDrive.
5. Copie War_Consequences.mod au même niveau que le dossier.
6. Active War Consequences dans le launcher.
7. Démarre une nouvelle partie de test : ne valide pas la version sur une sauvegarde importante.

CE QUI EST IMPLÉMENTÉ DANS LE SCRIPT
-----------------------------------
- Analyse périodique : le hook on_daily incrémente un compteur par pays et lance le calcul
  tous les trois jours de guerre.
- Six indices internes : militaire, économique, politique, social, territorial, administratif.
- Tirages séparés par famille d'événements : plusieurs événements de catégories différentes
  peuvent être déclenchés au même cycle si leurs conditions sont réunies.
- Cooldowns par famille.
- Facteurs de résilience génériques par régime, sans liste d'exceptions de pays.
- Événement de crise extrême qui appelle start_civil_war, l'effet natif de guerre civile.
  Le moteur du jeu crée la faction rebelle dynamiquement.
- Les idées temporaires donnent une durée limitée à plusieurs conséquences et permettent
  une récupération automatique à leur expiration.

SIMULÉ / PARTIEL
----------------
- Coup d'État : changement de régime via set_politics (fascisme ou démocratie selon le cas).
  Aucune junte ni dirigeant spécifique n'est nommé.
- Séparatisme : pression régionale simulée. Aucun pays/tag séparatiste n'est créé.
- Paix séparée : pression diplomatique simulée. Aucune conférence de paix ni paix blanche
  n'est déclenchée automatiquement.
- Gouvernement en exil : événement de continuité politique et idée temporaire uniquement.
  Le statut d'exil réel dépend des règles et DLC vanilla.
- Économie : les usines perdues sont mesurées par rapport au début de guerre, mais les
  usines endommagées, détruites puis reconstruites ne sont pas distinguées.
- Militaire : pas de compteur fiable de pertes humaines, d'équipement ou de divisions détruites.
- Territoire : l'occupation et la capitulation servent de proxys ; le contrôle de la capitale
  n'est pas encore mesuré indépendamment.

NON IMPLÉMENTÉ / EXPÉRIMENTAL
-----------------------------
- Création de pays séparatistes dynamiques : cela exige une stratégie de tags, de cores,
  de noms, de drapeaux, d'historiques et de répartition des États. Un tag dynamique n'est
  pas créé ici par simple événement.
- Paix séparée réelle avec sélection fiable d'un adversaire précis et traitement des alliés.
- Déplacement ou restauration manuelle d'un gouvernement en exil.
- Mesure fine des pertes humaines, stocks, ravitaillement et divisions.
- Validation des sprites d'idées dans le jeu.

NOTES TECHNIQUES IMPORTANTES
----------------------------
- start_civil_war est un effet natif de HOI4 documenté. Le script l'utilise uniquement
  quand l'indice politique dépasse un seuil, que la stabilité est très basse, et qu'aucune
  guerre civile WC n'a déjà été déclenchée.
- Les choix de révolte sont conditionnés par le régime en place. Les régimes ne correspondant
  pas aux quatre idéologies standards retombent sur une option sans guerre civile.
- Les indices sont des scores heuristiques 0-100, pas des données statistiques officielles.
- Le moteur on_daily est exécuté pour chaque pays chaque jour : la logique est courte et
  ne parcourt pas tous les États ou toutes les divisions, pour limiter le coût CPU.
- Les noms de sprites génériques peuvent varier selon les versions/DLC. Si un sprite est
  manquant, remplace picture par un sprite vanilla confirmé dans ton installation.

PROCÉDURE DE TEST
-----------------
1. Test syntaxe : démarre le jeu avec uniquement War Consequences activé.
2. Vérifie logs/error.log après le chargement du menu et d'une nouvelle partie.
3. Joue un pays en guerre pendant au moins 10 jours accélérés : les événements ne doivent
   être évalués qu'à intervalles de trois jours de guerre.
4. Teste un pays occupé et un pays à faible stabilité/soutien de guerre.
5. Teste une grande puissance et un petit pays : les seuils d'occupation sont des comptes
   de régions et peuvent être moins proportionnels pour les petits États.
6. Pour tester la guerre civile, baisse la stabilité et le soutien de guerre via les outils
   de test/console puis attends les scans. Utilise une sauvegarde jetable.
7. Vérifie qu'une guerre civile ne se déclenche pas deux fois et que le cooldown fonctionne.
8. Laisse expirer les idées temporaires et vérifie le retour des modificateurs.
9. Vérifie les logs :
   Documents/Paradox Interactive/Hearts of Iron IV/logs/error.log
   Documents/Paradox Interactive/Hearts of Iron IV/logs/game.log

DIFFÉRENCES AVEC LA 0.1
-----------------------
- Réécriture de la logique des indices et ajout de facteurs d'occupation/capitulation.
- Ajout d'un véritable appel à start_civil_war pour les crises politiques extrêmes.
- Ajout de profils de résilience génériques par idéologie.
- Localisations FR/EN complétées pour le nouvel événement.
- Clarification des limites : pas de fausse promesse de sécession, paix séparée ou exil réels.

ARBORESCENCE
------------
War_Consequences/
  descriptor.mod
  War_Consequences.mod
  README_FR.txt
  common/
    on_actions/wc_on_actions.txt
    scripted_effects/wc_crisis_engine.txt
    ideas/wc_ideas.txt
  events/wc_events.txt
  localisation/
    french/wc_l_french.yml
    english/wc_l_english.yml

SOURCES TECHNIQUES
------------------
- HOI4DOC — référence des effets, dont start_civil_war, white_peace et start_peace_conference :
  https://hoi4doc.dev/type/effect
- HOI4 Wiki — On actions (on_daily exécuté séparément pour chaque pays) :
  https://github.com/thelight0211/hoi4-wiki/blob/main/On%20actions%20-%20Hearts%20of%20Iron%204%20Wiki.md
- HOI4 Modding Wiki — start_civil_war :
  https://hoi4-modding.fandom.com/wiki/Effects/start_civil_war
