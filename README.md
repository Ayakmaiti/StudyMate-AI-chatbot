🎓 StudyMate AI
Un espace pour apprendre, comprendre et progresser en informatique et en intelligence artificielle.
Assistant pédagogique conversationnel · React · Vite · API FastAPI externe
Installation · Configuration · API · Structure · Dépannage
</div>

Présentation
StudyMate AI propose une interface de discussion pensée pour les étudiants qui souhaitent approfondir leurs connaissances en programmation, algorithmique, développement web et intelligence artificielle.
L’application réunit un espace de conversation, un historique consultable et un affichage Markdown adapté aux explications techniques et aux exemples de code. Son interface en français associe une page d’introduction animée et un espace de travail avec une barre latérale repliable.
Ce dépôt contient le frontend. La génération des réponses nécessite un backend séparé compatible avec le contrat API décrit ci-dessous. Le code du backend, sa configuration et le choix du modèle d’IA ne sont pas inclus ici.

Fonctionnalités
Fonctionnalité	Comportement
Accueil	Page d’introduction avec accès à l’espace de discussion et possibilité de la revoir
Conversations multiples	Création, sélection, renommage et suppression des conversations
Historique	Sauvegarde dans le navigateur via localStorage
Recherche	Filtrage des conversations par leur titre
Organisation	Regroupement par période et tri selon la dernière activité
Navigation	Barre latérale repliable et navigation adaptée aux petits écrans
Réponses Markdown	Titres, listes, liens, tableaux et blocs de code
Copie du code	Bouton de copie pour les blocs de code reconnus par le composant de rendu
Suggestions	Questions de démarrage pour lancer une discussion
Disponibilité	Vérification du backend toutes les 15 secondes
Gestion des erreurs	Messages d’erreur et possibilité de réessayer un envoi échoué
Saisie	Zone de texte à hauteur adaptative et raccourcis clavier


Exemples de questions
- « Explique-moi la différence entre une liste et un tuple en Python. »
- « Comment fonctionne un algorithme de recherche binaire ? »
- « Quelle est la différence entre apprentissage supervisé et non supervisé ? »
- « Donne-moi un exemple de route FastAPI. »
- « Explique les hooks React avec un exemple simple. »
Technologies
Technologie	Utilisation
React 19	Composants, état de l’interface et interactions
Vite 8	Serveur de développement et compilation de production
JavaScript / JSX	Logique applicative et composants
CSS	Mise en page, animations et adaptation aux écrans
react-markdown	Affichage des réponses Markdown
remark-gfm	Prise en charge des extensions GitHub Flavored Markdown
Fetch API	Communication HTTP avec le backend
localStorage	Conservation locale de l’historique et de certaines préférences
Oxlint	Analyse statique du code


Les versions et dépendances exactes sont disponibles dans package.json et package-lock.json.
Architecture
Le frontend envoie le message et l’identifiant de conversation au backend. Il affiche ensuite le champ response retourné par l’API et conserve les messages dans le navigateur.
- Interface React : navigation, saisie, historique, rendu des réponses et états de chargement.
- Service API : centralisation des appels HTTP dans src/services/api.js.
- Backend externe : traitement des messages, intégration du fournisseur d’IA et gestion éventuelle du contexte serveur.
Le stockage local de l’interface et la mémoire conversationnelle du backend sont deux mécanismes distincts. Restaurer l’historique dans le navigateur ne garantit pas que le backend possède encore le contexte associé.
Installation
Prérequis
- Git.
- Une version de Node.js compatible avec Vite 8 ; Node.js 22.12 ou supérieur dans la branche 22, ou une version LTS plus récente compatible, constitue une base de travail.
- npm.
- Un backend compatible pour obtenir les réponses de l’assistant.
1. Cloner le dépôt
git clone https://github.com/Ayakmaiti/StudyMate-AI-chatbot.git
cd StudyMate-AI-chatbot
2. Installer les dépendances
npm ci
Cette commande utilise le fichier de verrouillage pour reproduire les dépendances du projet.
3. Configurer le backend
Créer un fichier .env.local à la racine :
VITE_BACKEND_URL=http://127.0.0.1:8000
4. Démarrer le frontend
npm run dev
Ouvrir l’adresse affichée par Vite dans le terminal, généralement http://localhost:5173. Si ce port est occupé, Vite peut en sélectionner un autre.
L’interface peut être consultée sans backend ; les réponses IA et le statut de connexion nécessitent une API accessible.
Configuration
Variable	Valeur par défaut	Description
VITE_BACKEND_URL	http://127.0.0.1:8000	Adresse de base du backend, sans barre oblique finale


Redémarrer le serveur Vite après une modification du fichier d’environnement.
Les variables préfixées par VITE_ sont accessibles dans le navigateur : elles servent à la configuration publique. Ne jamais y placer une clé API privée. Les identifiants du fournisseur d’IA doivent être configurés côté backend.
Le fichier .env.local est ignoré par la règle *.local du .gitignore actuel. Cette protection ne couvre pas automatiquement tous les autres noms de fichiers .env.
Connexion entre frontend et backend
Le backend doit autoriser l’origine du frontend dans sa configuration CORS, par exemple http://localhost:5173 en développement. Si Vite utilise un autre port, adapter également cette origine.
Commandes disponibles
Commande	Description
npm ci	Installer les dépendances à partir du fichier de verrouillage
npm run dev	Lancer le serveur de développement
npm run lint	Analyser le code avec Oxlint
npm run build	Générer les fichiers de production dans dist/
npm run preview	Prévisualiser localement le résultat de la compilation


npm run preview permet de vérifier le build localement ; le déploiement final nécessite un hébergement adapté.
Contrat API
Les routes suivantes sont celles appelées par le frontend. Elles décrivent les attentes du client, et non une implémentation backend incluse dans ce dépôt.
Vérifier la disponibilité
GET /health
Réponse attendue pour afficher le backend comme disponible :
{
  "status": "ok"
}
La vérification est effectuée à l’ouverture de l’application puis toutes les 15 secondes. Elle ne garantit pas, à elle seule, que le fournisseur d’IA est opérationnel.
Envoyer un message
POST /chat
Content-Type: application/json
Corps de la requête :
{
  "conversation_id": "demo-studymate-001",
  "message": "Explique-moi les listes en Python."
}
Réponse attendue :
{
  "conversation_id": "demo-studymate-001",
  "response": "Une liste Python est une collection ordonnée et modifiable..."
}
Le frontend génère un identifiant pour chaque nouvelle conversation et le réutilise lors des échanges. Les espaces en début et en fin de message sont retirés avant l’envoi.
Supprimer le contexte serveur
DELETE /chat/{conversation_id}
Cette route est appelée lors de la suppression d’une conversation. Le client tente de lire une réponse JSON, mais tolère également une réponse vide.
La suppression de l’historique local peut aboutir même si la suppression côté serveur échoue : l’effacement côté backend doit donc être vérifié séparément si nécessaire.
Tester avec PowerShell
Avec un backend démarré sur le port 8000 :
Invoke-RestMethod -Uri "http://127.0.0.1:8000/health"

$body = @{
    conversation_id = "demo-studymate-001"
    message = "Bonjour, explique-moi les API."
} | ConvertTo-Json

Invoke-RestMethod `
    -Uri "http://127.0.0.1:8000/chat" `
    -Method Post `
    -ContentType "application/json; charset=utf-8" `
    -Body ([System.Text.Encoding]::UTF8.GetBytes($body))
Structure du projet
Chemin	Responsabilité
src/App.jsx	État global de l’interface, conversations, persistance et orchestration des requêtes
src/main.jsx	Point d’entrée React
src/services/api.js	Appels à /chat, /health et suppression du contexte
src/components/IntroScreen.jsx	Écran d’introduction
src/components/Sidebar.jsx	Historique, recherche et actions sur les conversations
src/components/Header.jsx	En-tête, disponibilité et accès aux actions
src/components/ChatArea.jsx	Affichage des messages, suggestions et erreurs
src/components/MessageItem.jsx	Rendu Markdown et copie des blocs de code
src/components/MessageInput.jsx	Saisie et raccourcis clavier
src/components/LimitsModal.jsx	Présentation du périmètre pédagogique et des limites d’utilisation
src/components/StudyMateLogo.jsx	Identité visuelle du chatbot
src/App.css, src/index.css	Styles de l’application
src/assets/	Ressources graphiques
public/	Ressources statiques publiques
vite.config.js	Configuration de Vite


Utilisation
1. Ouvrir l’application et passer l’écran d’introduction.
2. Choisir une question suggérée ou saisir son propre message.
3. Appuyer sur Entrée pour envoyer ; utiliser Maj + Entrée pour ajouter une ligne.
4. Créer une nouvelle conversation pour un autre sujet.
5. Rechercher une conversation par son titre dans la barre latérale.
6. Utiliser le menu de la conversation pour la renommer ou la supprimer.
7. En cas d’échec d’envoi, utiliser l’action de nouvelle tentative proposée dans l’interface.
Données et limites actuelles
- Les conversations sont conservées dans le localStorage du navigateur. Elles ne sont pas synchronisées entre appareils et peuvent disparaître si les données du site sont effacées.
- Un message envoyé est transmis au backend configuré. Le traitement ultérieur dépend de son implémentation et du fournisseur d’IA utilisé.
- Aucun système de connexion utilisateur ni de stockage distant des conversations n’est implémenté dans ce frontend.
- Les réponses sont reçues sous forme de JSON complet ; le client n’implémente pas de streaming de tokens.
- Le compteur de saisie affiche une référence de 4 000 caractères, mais le composant ne bloque pas actuellement les messages au-delà de cette longueur. Une limite effective doit être validée côté serveur.
- La fenêtre des limites présente un périmètre informatique et IA. Le filtrage effectif des sujets et les règles de génération doivent être appliqués par le backend.
- Les réponses peuvent contenir des erreurs : vérifier les explications et tester les exemples de code dans un environnement approprié.
Compilation et déploiement
npm run build
npm run preview
Le dossier dist/ contient les fichiers statiques à publier.
Pour un environnement hébergé :
1. Définir VITE_BACKEND_URL avec l’URL publique du backend avant la compilation.
2. Compiler le frontend.
3. Publier le contenu de dist/ sur l’hébergement choisi.
4. Autoriser le domaine du frontend dans les règles CORS du backend.
5. Utiliser HTTPS pour le frontend et le backend.
Les variables Vite sont intégrées au build. Une modification de l’adresse du backend nécessite une nouvelle compilation. L’adresse 127.0.0.1 désigne la machine du visiteur et ne convient pas pour accéder à un backend distant en production.
Dépannage
Problème	Vérifications
« Impossible de contacter le serveur »	Vérifier le démarrage du backend, son adresse et son port
Backend affiché indisponible	Vérifier que GET /health répond avec un statut HTTP de succès et {"status":"ok"}
Erreur CORS dans la console	Autoriser l’origine exacte du frontend côté backend
Erreur HTTP 422	Vérifier la validation de conversation_id et message côté serveur
Erreur HTTP 5xx	Consulter les logs du backend et vérifier sa configuration IA
Changement d’URL sans effet	Redémarrer Vite en développement, ou reconstruire le frontend en production
Historique absent	Vérifier le navigateur, l’origine utilisée et les données locales du site
Copie du code impossible	Vérifier les permissions du presse-papiers et utiliser un contexte sécurisé tel que HTTPS ou localhost
Échec de l’installation	Vérifier la compatibilité de Node.js et relancer npm ci depuis la racine du dépôt


Vérification manuelle
Après le démarrage du frontend et du backend :
- Vérifier l’accès à l’introduction et à l’espace de discussion.
- Envoyer une question et vérifier l’affichage de la réponse.
- Créer deux conversations et passer de l’une à l’autre.
- Renommer une conversation et la retrouver par recherche.
- Recharger la page pour vérifier la persistance locale.
- Supprimer une conversation et vérifier les effets côté client et côté serveur.
- Vérifier un tableau Markdown et un bloc de code avec copie.
- Tester la navigation sur un petit écran.
- Arrêter temporairement le backend pour vérifier les messages d’erreur.
Le dépôt ne définit actuellement pas de script de tests automatisés dans package.json.
Évolutions possibles
Pistes d’amélioration, non incluses dans les fonctionnalités actuelles :
- Authentification et synchronisation des conversations.
- Réponses progressives par streaming.
- Export et import de l’historique.
- Mise en évidence syntaxique du code.
- Tests automatisés des composants et des échanges API.
- Intégration de ressources pédagogiques vérifiées par recherche documentaire.
Auteure
Aya Kmaiti — GitHub
Projet : StudyMate-AI-chatbot
