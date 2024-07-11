import pygame
import sys
import random

# 初始化Pygame
pygame.init()

# 游戏窗口大小
WINDOW_WIDTH = 800
WINDOW_HEIGHT = 600

# 颜色定义
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
BLUE = (0, 0, 255)
RED = (255, 0, 0)

# 球的属性
BALL_RADIUS = 10
ball_speed_x = 5
ball_speed_y = 5

# 挡板的属性
PADDLE_WIDTH = 100
PADDLE_HEIGHT = 10
PADDLE_COLOR = RED
paddle_speed = 15

# 砖块的属性
BRICK_WIDTH = 80
BRICK_HEIGHT = 20
BRICK_COLOR = BLUE
BRICK_ROW_COUNT = 5
BRICK_COLUMN_COUNT = 10

# 创建游戏窗口
window = pygame.display.set_mode((WINDOW_WIDTH, WINDOW_HEIGHT))
pygame.display.set_caption('打砖块游戏')

# 球的初始位置和速度
ball_x = WINDOW_WIDTH // 2
ball_y = WINDOW_HEIGHT // 2 + 100

# 挡板的初始位置
paddle_x = (WINDOW_WIDTH - PADDLE_WIDTH) // 2
paddle_y = WINDOW_HEIGHT - PADDLE_HEIGHT - 20

# 创建砖块列表
bricks = []
for row in range(BRICK_ROW_COUNT):
    for column in range(BRICK_COLUMN_COUNT):
        brick = pygame.Rect(column * (BRICK_WIDTH + 5) + 30, row * (BRICK_HEIGHT + 5) + 50, BRICK_WIDTH, BRICK_HEIGHT)
        bricks.append(brick)

# 游戏主循环
while True:
    window.fill(BLACK)  # 清屏
    
    # 事件处理
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    # 按键处理
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        paddle_x -= paddle_speed
    if keys[pygame.K_RIGHT]:
        paddle_x += paddle_speed
    
    # 球的移动和碰撞检测
    ball_x += ball_speed_x
    ball_y += ball_speed_y
    
    # 球与边界碰撞检测
    if ball_x <= 0 or ball_x >= WINDOW_WIDTH:
        ball_speed_x = -ball_speed_x
    if ball_y <= 0:
        ball_speed_y = -ball_speed_y
    
    # 球与挡板碰撞检测
    ball_rect = pygame.Rect(ball_x - BALL_RADIUS, ball_y - BALL_RADIUS, BALL_RADIUS * 2, BALL_RADIUS * 2)
    paddle_rect = pygame.Rect(paddle_x, paddle_y, PADDLE_WIDTH, PADDLE_HEIGHT)
    if ball_rect.colliderect(paddle_rect):
        ball_speed_y = -ball_speed_y
    
    # 球与砖块碰撞检测
    for brick in bricks[:]:
        if ball_rect.colliderect(brick):
            bricks.remove(brick)
            ball_speed_y = -ball_speed_y
    
    # 绘制球、挡板和砖块
    pygame.draw.circle(window, WHITE, (ball_x, ball_y), BALL_RADIUS)
    pygame.draw.rect(window, PADDLE_COLOR, (paddle_x, paddle_y, PADDLE_WIDTH, PADDLE_HEIGHT))
    for brick in bricks:
        pygame.draw.rect(window, BRICK_COLOR, brick)
    
    # 判断游戏结束条件
    if ball_y >= WINDOW_HEIGHT:
        font = pygame.font.Font(None, 74)
        text = font.render("Game Over", True, WHITE)
        window.blit(text, (250, 300))
        pygame.display.flip()
        pygame.time.wait(2000)
        pygame.quit()
        sys.exit()

    # 刷新屏幕
    pygame.display.flip()
    pygame.time.delay(30)
