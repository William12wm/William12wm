import pygame
import sys

# Initialize Pygame
pygame.init()

# Screen settings
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Pixelated Clicker Game with Ground and Sky")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
BLUE = (135, 206, 235)  # Sky blue
BROWN = (139, 69, 19)   # Ground brown
RED = (255, 0, 0)

# Fonts
font = pygame.font.Font(pygame.font.match_font('arial'), 24)

# Variables
points = 0
money_per_click = 1
rebirth_cost = 1000
rebirth_multiplier = 1
upgrade_cost = 50
upgrade_level = 0
is_jumping = False
jump_height = 10
ground_level = HEIGHT - 100
player_y = ground_level - 50
jump_velocity = jump_height

# Character settings
player_size = 50
player = pygame.Rect(WIDTH // 2 - player_size // 2, player_y, player_size, player_size)

# Game loop
clock = pygame.time.Clock()
running = True

while running:
    screen.fill(BLUE)  # Sky background

    # Draw ground
    pygame.draw.rect(screen, BROWN, (0, ground_level, WIDTH, HEIGHT - ground_level))

    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            # Left click: Jump and gain points
            if not is_jumping:
                is_jumping = True
            points += money_per_click
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r and points >= rebirth_cost:
                # Rebirth: Increase multiplier and reset points
                points -= rebirth_cost
                rebirth_multiplier += 1
                money_per_click = rebirth_multiplier
                rebirth_cost *= 2
            elif event.key == pygame.K_u and points >= upgrade_cost:
                # Upgrade: Increase money per click
                points -= upgrade_cost
                upgrade_level += 1
                money_per_click += 1
                upgrade_cost = int(upgrade_cost * 1.5)

    # Jumping logic
    if is_jumping:
        player.y -= jump_velocity
        jump_velocity -= 1
        if jump_velocity < -jump_height:
            is_jumping = False
            jump_velocity = jump_height
            player.y = player_y

    # Draw player
    pygame.draw.rect(screen, GREEN, player)

    # Draw UI
    points_text = font.render(f"Points: {points}", True, BLACK)
    screen.blit(points_text, (10, 10))

    money_text = font.render(f"Money/Click: {money_per_click}", True, BLACK)
    screen.blit(money_text, (10, 50))

    rebirth_text = font.render(f"Press R to Rebirth (Cost: {rebirth_cost})", True, RED)
    screen.blit(rebirth_text, (10, 90))

    upgrade_text = font.render(f"Press U to Upgrade (Cost: {upgrade_cost}, Level: {upgrade_level})", True, BLACK)
    screen.blit(upgrade_text, (10, 130))

    # Update screen
    pygame.display.flip()
    clock.tick(30)

pygame.quit()
sys.exit()
