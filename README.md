import pygame
import random

# Initialize Pygame
pygame.init()

# Set up the game window
WIDTH, HEIGHT = 800, 600
WINDOW = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Tetris")

# Define colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
CYAN = (0, 255, 255)
MAGENTA = (255, 0, 255)
YELLOW = (255, 255, 0)
ORANGE = (255, 165, 0)

# Define the block shapes and their colors
SHAPES = [
    [[1, 1, 1, 1]],                     # I-shape
    [[1, 1], [1, 1]],                   # O-shape
    [[1, 1, 0], [0, 1, 1]],             # Z-shape
    [[0, 1, 1], [1, 1, 0]],             # S-shape
    [[1, 1, 1], [0, 1, 0]],             # T-shape
    [[1, 1, 1], [0, 0, 1]],             # L-shape
    [[1, 1, 1], [1, 0, 0]],             # J-shape
]

COLORS = [CYAN, YELLOW, GREEN, RED, BLUE, ORANGE, MAGENTA]

# Set up the game grid
GRID_WIDTH, GRID_HEIGHT = 10, 20
GRID_SIZE = 30
GRID_OFFSET_X, GRID_OFFSET_Y = 50, 50
grid = [[BLACK for _ in range(GRID_WIDTH)] for _ in range(GRID_HEIGHT)]

# Set up the current falling piece
current_piece = random.choice(SHAPES)
current_piece_color = random.choice(COLORS)
piece_x = GRID_WIDTH // 2 - len(current_piece[0]) // 2
piece_y = 0

# Game variables
score = 0
clock = pygame.time.Clock()
fall_time = 0
fall_speed = 0.5

# Game loop
running = True
while running:
    WINDOW.fill(BLACK)

    # Handle events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                piece_x -= 1
            elif event.key == pygame.K_RIGHT:
                piece_x += 1
            elif event.key == pygame.K_DOWN:
                fall_speed = 0.1

        if event.type == pygame.KEYUP:
            if event.key == pygame.K_DOWN:
                fall_speed = 0.5

    # Move the current piece
    fall_time += clock.get_rawtime()
    if fall_time / 1000 >= fall_speed:
        piece_y += 1
        fall_time = 0

    # Check collision
    if piece_y + len(current_piece) > GRID_HEIGHT:
        piece_y = GRID_HEIGHT - len(current_piece)
    for row in range(len(current_piece)):
        for col in range(len(current_piece[row])):
            if current_piece[row][col] and grid[piece_y + row][piece_x + col] != BLACK:
                piece_y -= 1
                for row in range(len(current_piece)):
                    for col in range(len(current_piece[row])):
                        if current_piece[row][col]:
                            grid[piece_y + row][piece_x + col] = current_piece_color
                current_piece = random.choice(SHAPES)
                current_piece_color = random.choice(COLORS)
                piece_x = GRID_WIDTH // 2 - len(current_piece[0]) // 2
                piece_y = 0
                score += 10

    # Draw the grid
    for row in range(GRID_HEIGHT):
        for col in range(GRID_WIDTH):
            pygame.draw.rect(WINDOW, grid[row][col], (GRID_OFFSET_X + col * GRID_SIZE, GRID_OFFSET_Y + row * GRID_SIZE, GRID_SIZE, GRID_SIZE))
            pygame.draw.rect(WINDOW, WHITE, (GRID_OFFSET_X + col * GRID_SIZE, GRID_OFFSET_Y + row * GRID_SIZE, GRID_SIZE, GRID_SIZE), 1)

    # Draw the current piece
    for row in range(len(current_piece)):
        for col in range(len(current_piece[row])):
            if current_piece[row][col]:
                pygame.draw.rect(WINDOW, current_piece_color, (GRID_OFFSET_X + (piece_x + col) * GRID_SIZE, GRID_OFFSET_Y + (piece_y + row) * GRID_SIZE, GRID_SIZE, GRID_SIZE))
                pygame.draw.rect(WINDOW, WHITE, (GRID_OFFSET_X + (piece_x + col) * GRID_SIZE, GRID_OFFSET_Y + (piece_y + row) * GRID_SIZE, GRID_SIZE, GRID_SIZE), 1)

    # Display score
    font = pygame.font.SysFont(None, 36)
    text = font.render("Score: " + str(score), True, WHITE)
    WINDOW.blit(text, (50, 20))

    pygame.display.update()
    clock.tick(30)

# Quit the game
pygame.quit()
