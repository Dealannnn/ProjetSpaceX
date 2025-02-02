🚀 SpaceX 
1. Introduction
Brève présentation du projet :

Objectif : Afficher les lancements de SpaceX avec des filtres et un compte à rebours.

Technologies utilisées : Vue.js (Composition API), TypeScript, API SpaceX.

2. Fonctionnalités développées
Affichage du prochain lancement avec un décompte en temps réel.
Liste des 10 derniers lancements avec filtres (tous, réussis, échoués).
Un modal affichant les détails d’un lancement.
Affichage dynamique des données via l’API SpaceX.

4. Difficultés rencontrées et solutions
Problème : Récupération et filtrage des données
Solution : Utilisation de computed() pour gérer dynamiquement les filtres.

Problème : Affichage en temps réel du décompte
Solution : setInterval() mis à jour toutes les secondes.

Problème : Gestion du modal
Solution : Utilisation de ref() pour stocker les données du lancement sélectionné.

6. Choix techniques
Vue.js + Composition API : Simplicité et réactivité.
TypeScript : Typage des données pour éviter les erreurs.
API SpaceX : Accès aux données en temps réel.

8. Ressources utilisées
Documentation Vue.js
API SpaceX
Forums (Vue.js Community)
