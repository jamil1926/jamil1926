
import pygame
import random

# Colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
RED = (255, 0, 0)
GREEN = (0, 255, 0)

# Screen dimensions
WIDTH = 800
HEIGHT = 600

# Car class
class Car(pygame.sprite.Sprite):
    def __init__(self, x, y, color):
        super().__init__()
        self.image = pygame.Surface((35, 20))
        self.image.fill(color)
        self.rect = self.image.get_rect(topleft=(x, y))
        self.speed = 5

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT]:
            self.rect.x += self.speed
        # Keep car on screen
        if self.rect.x < 0:
            self.rect.x = 0
        if self.rect.x > WIDTH - self.rect.width:
            self.rect.x = WIDTH - self.rect.width

# Obstacle class
class Obstacle(pygame.sprite.Sprite):
    def __init__(self, image_path, x, y):
        super().__init__()
        self.image = pygame.image.load(image_path)
        self.rect = self.image.get_rect(topleft=(x, y))
        self.speed = random.randint(3, 5)  # Vary obstacle speeds

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            self.kill()

# Initialize Pygame
pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Car Racing Game")
clock = pygame.time.Clock()

# Create car
car = Car(WIDTH // 2 - 17, HEIGHT - 50, RED)

# Create obstacle group
obstacles = pygame.sprite.Group()

# Main game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Update game objects
    car.update()
    obstacles.update()

    # Spawn new obstacles
    if random.randint(1, 60) == 1:  # Adjust spawn rate
        obstacle = Obstacle("path/to/obstacle_image.png", random.randrange(WIDTH - 35), -100)
        obstacles.add(obstacle)

    # Check for collisions
    collisions = pygame.sprite.spritecollide(car, obstacles, False)
    if collisions:
        print("Collision!")
        # Handle collision (e.g., game over, reset game)

    # Draw game objects
    screen.fill(BLACK)
    screen.blit(car.image, car.rect)
    obstacles.draw(screen)

    pygame.display.flip()
    clock.tick(60)

# Quit Pygame
pygame.quit()
