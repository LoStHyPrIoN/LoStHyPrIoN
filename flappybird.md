import pygame
import sys
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 600, 400
FPS = 30
GRAVITY = 1
BIRD_SPEED = 5
PIPE_SPEED = 5
PIPE_GAP = 100

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# Create the game window
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Flappy Bird")

# Load images
bird_img = pygame.image.load("bird.png")
pipe_img = pygame.image.load("pipe.png")
background_img = pygame.image.load("background.png")

# Scale images
bird_img = pygame.transform.scale(bird_img, (50, 50))
pipe_img = pygame.transform.scale(pipe_img, (50, HEIGHT))

# Bird class
class Bird(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = bird_img
        self.rect = self.image.get_rect()
        self.rect.center = (100, HEIGHT // 2)
        self.velocity = 0

    def update(self):
        self.velocity += GRAVITY
        self.rect.y += self.velocity

# Pipe class
class Pipe(pygame.sprite.Sprite):
    def __init__(self, x):
        super().__init__()
        self.image = pipe_img
        self.rect = self.image.get_rect()
        self.rect.topleft = (x, 0)

    def update(self):
        self.rect.x -= PIPE_SPEED

# Create sprite groups
all_sprites = pygame.sprite.Group()
pipes = pygame.sprite.Group()

# Create bird
bird = Bird()
all_sprites.add(bird)

# Game loop
clock = pygame.time.Clock()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            bird.velocity = -BIRD_SPEED

    # Update
    all_sprites.update()

    # Spawn pipes
    if random.randint(1, 75) == 1:
        new_pipe = Pipe(WIDTH)
        pipes.add(new_pipe)
        all_sprites.add(new_pipe)

    # Check for collisions with pipes
    hits = pygame.sprite.spritecollide(bird, pipes, False)
    if hits:
        pygame.quit()
        sys.exit()

    # Remove off-screen pipes
    for pipe in pipes.copy():
        if pipe.rect.right < 0:
            pipes.remove(pipe)
            all_sprites.remove(pipe)

    # Draw
    screen.fill(WHITE)
    screen.blit(background_img, (0, 0))
    all_sprites.draw(screen)

    # Flip display
    pygame.display.flip()

    # Cap the frame rate
    clock.tick(FPS)
