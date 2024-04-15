 import pygame
import random

# Initialisation de Pygame
pygame.init()

# Définition de la taille de l'écran
largeur = 800
hauteur = 600
ecran = pygame.display.set_mode((largeur, hauteur))
pygame.display.set_caption("Jeu de Course")

# Définition des couleurs
BLANC = (255, 255, 255)
NOIR = (0, 0, 0)
ROUGE = (255, 0, 0)

# Définition des variables du jeu
score = 0
vitesse = 5

# Classe pour la voiture du joueur
class Voiture(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 80))
        self.image.fill(ROUGE)
        self.rect = self.image.get_rect()
        self.rect.centerx = largeur // 2
        self.rect.bottom = hauteur - 20
        self.vx = 0

    def update(self):
        self.vx = 0
        touches = pygame.key.get_pressed()
        if touches[pygame.K_LEFT]:
            self.vx = -vitesse
        if touches[pygame.K_RIGHT]:
            self.vx = vitesse
        self.rect.x += self.vx
        if self.rect.left < 0:
            self.rect.left = 0
        if self.rect.right > largeur:
            self.rect.right = largeur

# Classe pour les obstacles
class Obstacle(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((random.randint(20, 50), random.randint(20, 50)))
        self.image.fill(BLANC)
        self.rect = self.image.get_rect()
        self.rect.x = random.randrange(largeur - self.rect.width)
        self.rect.y = random.randrange(-100, -40)
        self.vy = random.randrange(1, 8)

    def update(self):
        self.rect.y += self.vy
        if self.rect.top > hauteur + 10:
            self.rect.x = random.randrange(largeur - self.rect.width)
            self.rect.y = random.randrange(-100, -40)
            self.vy = random.randrange(1, 8)

# Groupe de sprites
tous_les_sprites = pygame.sprite.Group()
voiture = Voiture()
tous_les_sprites.add(voiture)
obstacles = pygame.sprite.Group()

# Boucle de jeu
continuer = True
horloge = pygame.time.Clock()
while continuer:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            continuer = False

    # Génération des obstacles
    if len(obstacles) < 10:
        nouvel_obstacle = Obstacle()
        obstacles.add(nouvel_obstacle)
        tous_les_sprites.add(nouvel_obstacle)

    # Mise à jour des sprites
    tous_les_sprites.update()

    # Collision entre la voiture et les obstacles
    collisions = pygame.sprite.spritecollide(voiture, obstacles, True)
    if collisions:
        score -= 1

    # Affichage du score
    ecran.fill(NOIR)
    font = pygame.font.Font(None, 36)
    texte_score = font.render("Score: " + str(score), True, BLANC)
    ecran.blit(texte_score, (10, 10))

    # Actualisation de l'écran
    tous_les_sprites.draw(ecran)
    pygame.display.flip()

    # Régulation de la vitesse de rafraîchissement
    horloge.tick(30)

# Fin du jeu
pygame.quit()
