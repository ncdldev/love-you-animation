# love-you-animation
A Python animation that creates a heart 
pip install pygame
python heart.py
using "Love You" text.
import pygame
import math
import random
import sys

pygame.init()

# Window
WIDTH, HEIGHT = 900, 700
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Love You")

clock = pygame.time.Clock()

# Fonts
small_font = pygame.font.SysFont("arial", 16, bold=True)
big_font = pygame.font.SysFont("arial", 72, bold=True)

# Colors
BLACK = (0, 0, 0)
BLUE = (40, 130, 255)
WHITE = (255, 255, 255)

# Heart equation
def heart(t):
    x = 16 * math.sin(t) ** 3
    y = (
        13 * math.cos(t)
        - 5 * math.cos(2 * t)
        - 2 * math.cos(3 * t)
        - math.cos(4 * t)
    )

    scale = 22

    return (
        WIDTH // 2 + x * scale,
        HEIGHT // 2 - y * scale
    )


# Create heart points
points = []

for i in range(180):
    t = (2 * math.pi / 180) * i

    x, y = heart(t)

    # Add several particles around each point
    for _ in range(3):
        points.append((
            x + random.randint(-18, 18),
            y + random.randint(-12, 12)
        ))


# Shuffle slightly so the heart is drawn progressively
random.shuffle(points)

particles = []

# Animation control
index = 0
finished = False
finish_timer = 0

running = True

while running:

    dt = clock.tick(60) / 1000

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_ESCAPE:
                running = False

    screen.fill(BLACK)

    # Create new particles
    if not finished:

        for _ in range(4):

            if index < len(points):

                x, y = points[index]

                particles.append({
                    "x": x + random.randint(-250, 250),
                    "y": y + random.randint(-180, 180),
                    "target_x": x,
                    "target_y": y,
                    "alpha": 0,
                    "speed": random.uniform(3, 6),
                    "text": "Love You"
                })

                index += 1

            else:
                finished = True

    # Update particles
    for p in particles:

        dx = p["target_x"] - p["x"]
        dy = p["target_y"] - p["y"]

        distance = math.sqrt(dx * dx + dy * dy)

        if distance > 1:
            p["x"] += dx * 0.08
            p["y"] += dy * 0.08

        p["alpha"] = min(255, p["alpha"] + 8)

        # Glow
        glow = small_font.render(p["text"], True, BLUE)

        glow.set_alpha(int(p["alpha"] * 0.25))

        screen.blit(
            glow,
            (p["x"] + 3, p["y"] + 3)
        )

        # Main text
        text = small_font.render(p["text"], True, BLUE)
        text.set_alpha(int(p["alpha"]))

        screen.blit(
            text,
            (p["x"], p["y"])
        )


    # When the heart is completed
    if finished:

        finish_timer += dt

        if finish_timer > 1.5:

            # Large white "Love You"
            final_text = big_font.render(
                "Love You",
                True,
                WHITE
            )

            final_rect = final_text.get_rect(
                center=(WIDTH // 2, HEIGHT // 2 + 10)
            )

            # Soft glow
            glow_surface = big_font.render(
                "Love You",
                True,
                (80, 80, 80)
            )

            glow_surface.set_alpha(100)

            for offset in range(8, 0, -2):
                glow_rect = glow_surface.get_rect(
                    center=(
                        WIDTH // 2 + offset,
                        HEIGHT // 2 + 10 + offset
                    )
                )

                screen.blit(
                    glow_surface,
                    glow_rect
                )

            screen.blit(
                final_text,
                final_rect
            )


    pygame.display.flip()


pygame.quit()
sys.exit()
