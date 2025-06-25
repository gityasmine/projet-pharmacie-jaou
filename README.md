# mini-pharmacie-vue
objectif : création d'application de pharmacie, une gestion de médicaments

Cette application Vue.js permet la gestion d’un stock de médicaments à travers une interface web simple. Elle interagit avec une API distante (https://apipharmacie.pecatte.fr/) et propose les principales opérations create, read, update et delete.

Les fonctionnalités sont les suivantes : 

  - Une liste des médicaments disponibles, affichés sous forme de cartes.
  - Une barre de recherche filtrant les médicaments par leur nom.
  - Formulaire d’ajout d’un nouveau médicament avec : nom
  - Incrémentation et décrémentation de la quantité.
  - Empêche de descendre en dessous de zéro.
  - Suppression d’un médicament de la base.

Compte tenu de l'architecture technique : 

  - Fichier Medicament.js :

Dans le fichier Medicament.js, on trouve une classe Medicament qui sert de modèle pour représenter un médicament dans l’application. Cette classe est construite à partir d’un objet JSON, ce qui est pratique parce que les données des médicaments viennent généralement d’une API sous ce format. Quand on crée une instance de Medicament, on initialise ses propriétés principales comme l’identifiant (id), la dénomination du médicament (denomination), la forme pharmaceutique (formepharmaceutique), la photo (photo) et la quantité disponible (qte). En résumé, cette classe nous permet de manipuler facilement les données des médicaments dans le reste de l’application.

  - Fichier App.vue :

Le fichier App.vue constitue le point d’entrée principal de l’application Vue.js. 
Dans son template, on trouve une structure simple mais efficace : une barre de navigation en haut qui affiche le titre « Pharmacie : Gestion des Médicaments », ce qui permet à l’utilisateur de comprendre immédiatement le but de l’application. 
Juste en dessous, deux composants enfants sont intégrés. Le premier, AjoutMedicament, est dédié à l’ajout de nouveaux médicaments dans la base de données. Il émet un événement medicamentAjoute chaque fois qu’un médicament est ajouté, ce qui permet de notifier le parent. Le second composant, AffichMedicament, est responsable de l’affichage de la liste des médicaments existants.

Pour gérer la communication entre ces composants, on utilise une référence affichageMedicament liée au composant d’affichage. La fonction getMedicament est déclenchée à chaque ajout, elle invoque alors une méthode sur AffichMedicament pour rafraîchir la liste visible des médicaments. Ce mécanisme assure que l’interface reste toujours à jour sans recharger toute la page.

  - Fichier Medicament.vue :

Le composant Medicament.vue joue le rôle de conteneur principal pour gérer la liste des médicaments et l’ajout de nouveaux médicaments. Dans son template, il intègre deux composants enfants : AjoutMedicament et AffichMedicament. Le composant AjoutMedicament est chargé de fournir le formulaire pour ajouter un nouveau médicament, tandis que AffichMedicament est responsable de l’affichage de la liste des médicaments existants.

Dans la partie script, on utilise la composition API de Vue avec le mode setup. On déclare une référence affichageMedicament qui permet de récupérer l’instance du composant AffichMedicament. Cela permet ensuite d’appeler directement la méthode getMedicament de ce composant, pour rafraîchir la liste des médicaments.

La fonction getMedicament est déclenchée lorsqu’un événement personnalisé medicamentAjoute est émis par le composant AjoutMedicament. Cette fonction appelle alors la méthode getMedicament sur AffichMedicament via la référence, afin de mettre à jour la liste visible à l’utilisateur après l’ajout d’un médicament.

  - Fichier AjoutMedicament.vue :

Le composant AjoutMedicament.vue permet d’ajouter un nouveau médicament via un formulaire utilisateur. Il contient plusieurs champs contrôlés par la directive v-model pour capturer la dénomination, la forme pharmaceutique, la quantité, ainsi qu’une photo optionnelle du médicament.

La gestion des données se fait via la Composition API avec une référence réactive nouveauMedicament qui stocke temporairement les informations saisies dans le formulaire. Lorsque l’utilisateur soumet le formulaire, la fonction ajouterMedicament est déclenchée.

Cette fonction effectue une requête HTTP POST vers l'API distante, dont l’URL est construite avec l’identifiant de la pharmacie(pour moi l'id 9). Les données sont envoyées au format JSON, et la réponse est analysée. En cas de succès (indiqué par un status égal à 1 dans la réponse), un message de confirmation est affiché, le formulaire est réinitialisé, et un événement personnalisé medicamentAjoute est émis. Cet événement permet au composant parent de rafraîchir la liste des médicaments affichés.

Le composant gère également le chargement d’un fichier image pour la photo du médicament. L’image sélectionnée par l’utilisateur est convertie en chaîne Base64 via un FileReader et stockée dans l’objet nouveauMedicament, prête à être envoyée à l’API.

Enfin, un message informatif est affiché pour notifier l’utilisateur du succès ou d’une éventuelle erreur lors de l’ajout du médicament.

  - Fichier AffichMedicament.vue :

Le composant AffichMedicament.vue affiche une liste de médicaments récupérés depuis une API. Il propose un champ de recherche qui permet de filtrer dynamiquement les médicaments en fonction de leur nom. Chaque médicament est présenté sous forme d’une carte affichant sa photo si elle est disponible, sa dénomination, sa forme pharmaceutique ainsi que sa quantité.

Pour modifier la quantité d’un médicament, l’utilisateur peut cliquer sur les boutons “+1” ou “-1” qui envoient une requête PUT à l’API pour mettre à jour la quantité. Si la modification est réussie, la quantité affichée est également mise à jour localement. Il est également possible de supprimer un médicament en cliquant sur un bouton “Supprimer”, qui demande d’abord une confirmation avant d’envoyer une requête DELETE à l’API. En cas de succès, le médicament est retiré de la liste affichée.

Le composant récupère initialement la liste des médicaments grâce à la méthode getMedicament, appelée au moment du montage du composant (hook onMounted = Le hook onMounted sert à dire à Vue de faire quelque chose juste après que le composant soit affiché à l’écran. C’est comme quand on attend que la page soit complètement chargée pour lancer une action. Ici, on utilise onMounted pour appeler la fonction getMedicament, qui va chercher la liste des médicaments depuis l’API. Comme ça, dès que le composant est visible, la liste s’affiche automatiquement).
Cette méthode est également exposée via defineExpose, ce qui permet au composant parent de l’appeler pour rafraîchir la liste lorsque nécessaire, par exemple après l’ajout d’un nouveau médicament.

Pour afficher la photo d’un médicament, la fonction getImageUrl construit l’URL complète de l’image à partir de la chaîne retournée par l’API.



    
