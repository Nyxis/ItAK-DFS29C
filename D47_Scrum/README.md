# D47 - Scrum / Méthodes agiles

## Spécifications fonctionnelles - Application Black Bunny

L'application de réservation pour le bar à jeux "Black Bunny" est conçue pour offrir une expérience utilisateur optimale tant pour les clients que pour le personnel administratif.

Centrée autour de la gestion flexible des tables et des salles, l'application prend en compte les spécificités des réservations, y compris les réservations de jeux, en garantissant un traitement adapté pour les jeux experts.
Les clients peuvent réserver à l'avance selon des créneaux prédéfinis, avec des vérifications automatiques pour éviter les doubles réservations ou les réservations consécutives.
Les comportements passés des clients influent également sur les réservations futures, assurant une meilleure gestion des no-shows.
Enfin, le personnel a la capacité de gérer les salles, les tables et les horaires disponibles, tout en ayant une vue d'ensemble des réservations en temps réel.

### Configuration des salles

Ajout, suppression, et modification des salles.
Chaque salle possède un identifiant unique et un nom.

_Exemple_ : Salle 1 - "Salon Principal", Salle 2 - "Espace Jeux".

### Association tables-salles
Ajout ou modification d'une table pour spécifier sa salle.
Le système permet d'associer des types de tables spécifiques à des salles.

_Exemple_ : Dans "Salon Principal", 3 tables de 4 et 2 tables de 6. Dans "Espace Jeux", 2 tables de 2, 1 table de 4, et 1 table de 8.

### Jeux

Ajout, suppression, et modification des jeux.
Chaque jeu peut être classifié selon son type (enfant, famille, initié, expert, ambiance...). La liste des types pourra évoluer.

Si un jeu expert est réservé, le système devra en tenir compte lors du calcul de l'heure de fin théorique, il ajoutera une heure.

_Exemple_ : "Terraforming Mars" est un jeu expert de 2-5 joueurs. Si 5 personnes le réservent à 18h30, une table de 7 (5+2) sera nécessaire et devra être marquée comme occupée une heure de plus.

### Événements

Ajout, suppression, et modification d'événements.

Certains pourront être récurrents (toutes les semaines, mois etc...)

Les événements devront être attribués à une ou plusieurs tables ou une ou plusieurs salles. Ils auront également un nombre maximum de participants qui peut ne pas être égal à la capacité des tables.

Un événement peut avoir une durée théorique.

La réservation d'un événement peut être soumis au paiement d'un acompte, il est défini à la création de cet événement.

### Réservation

Les horaires disponibles à la réservation sont définis par le personnel.

#### Demande de réservation

Le client ou un opérateur entre les informations du client nécessaires : nom, numéro de téléphone, e-mail, date, heure souhaitée de réservation, nombre de participants.

Un même utilisateur ne peut pas avoir deux réservations le même jour.

Il est impossible de réserver un créneau dans moins d'1h30.
Il est impossible de réserver une table pour des groupes de 9 ou plus.
Il est possible de réserver une salle entière pour des groupes de 10 ou plus, mais avec un acompte de 250€.

Le client peut fournir des informations complémentaires pour indiquer qu'il compte commander un repas, ou la présence d'enfants mineurs ou d'animaux domestiques.

Sélection éventuelle d'un jeu, d'un événement ou d'un animateur.

Dans le cas de la sélection d'un événement, le client ne pourra l'afficher que si la taille de son groupe est inférieure ou égale aux places non réservées restantes (une place n'est pas réservée tant que la réservation n'est pas confirmée ou la caution déposée).

Un animateur peut être réservé pour 1 ou 2 heures pour des occasions spéciales, comme un anniversaire ou du jeu de rôle (le client pourra saisir des détails dans un champ texte), si il n'est pas déjà attribué à une autre réservation validée ce jour ci. La réservation d'un animateur dédié à la table génère une demande d'acompte de 35€ par heure demandée.


#### Réservation automatique : proposition des créneaux

Hors événements, pour des réservations de plus de 24h à l'avance, le système propose des créneaux de réservation aux clients et accepte leurs demandes de réservation automatiquement.

Le client se voit proposer une liste de créneaux espacés de 15 minutes pour lesquels il est possible de réserver.

Une table est disponible si :
  - elle n'est pas occupée à l'heure demandée : chaque réservation a une heure de fin théorique calculée à heure d'arrivée + 2h30 (+ éventuels modificateurs pour jeux experts notemment), la table est considérée comme disponible que si la date de réservation est supérieure à la date de fin théorique renseignée.
  - elle dispose de la capacité suffisante pour le groupe. Dans le cas où la capacité de la table n'est pas suffisante, si une autre table est disponible dans la même salle dont la somme des capacités ne dépasse pas taille de groupe + 2, le système attribuera les deux tables.

_Exemple_ : Jean-Kevin souhaite réserver à 18h pour 8, aucune table de 8 n'est disponible à 18h, mais à 18h15 deux tables de 4 sont disponibles. Le système lui affiche donc 18h15 / 18h30 / 18h15 / 19h.

Une fois que le client accepte un créneau, la réservation est automatiquement acceptée par le système.

Pour les événements, si il reste des places, le système accepte la demande automatiquement.

#### Réservation manuelle

Pour les réservations du jour même, hors événements, le système n'attribuera pas automatiquement de table, ce sont les opérationnels qui accepterons la réservation en attribuant une ou plusieurs tables.

Les opérateurs peuvent activer la réservation automatique au jour le jour.

#### Confirmation de la réservation

Une fois la demande de réservation acceptée, le client doit confirmer sa réservation.

Un acompte est requis si :
  - l'événement sélectionné demande un acompte
  - une salle entière est réservée
  - un animateur est réservé

Si aucun acompte est requis, une caution est requise si :
  - le nombre de participants est > 5 : 7€
  - le client a déjà au moins une réservation en "no-show" : 10€

Dans tous les cas, un lien est généré et envoyé au client par sms et email pour qu'il confirme sa réservation en payant son acompte si requis, déposant sa caution si requis, ou simplement en cochant la case "je confirme ma réservation" dans les autres cas.

Les dépots de caution et d'acompte se passent via Stripe.

Si la réservation n'a pas été confirmée dans les 30min, un email/sms de relance est envoyé automatiquement.

#### Retard

Après 15min de retard, un sms de rappel de réservation est envoyé.

Si le groupe du client est en retard, il a la possibilité de repousser sa réservation jusqu'à 30 minutes après l'heure de réservation en utilisant le lien généré pour la confirmation.

#### Annulation

Si la réservation n'est pas confirmée 30min après la 1e relance, ou 1h avant la réservation, la réservation est annulée automatiquement.

Le client peut annuler sa réservation à tout moment en utilisant le lien généré pour la confirmation.

Si le client annule moins de deux heures avant sa date de réservation, les opérateurs pourront saisir manuellement la caution.

Si la réservation n'est pas placée après 30min après son heure de réservation initiale, la réservation est annulée automatiquement et passe en état "no-show", les opérateurs pourront saisir manuellement la caution.

Les opérateurs peuvent annuler manuellement une réservation à tout moment.

Dans tous les cas d'annulation, le client reçoit un email/sms les informant de l'annulation de sa réservation.

Dans le cas des acomptes, ils seront toujours conservés.

#### Réservation honnorée

Quand le client arrive, l'opérateur marque la réservation en "placé" et en "terminée" à son départ, en ayant la possibilité de noter un commentaire.
