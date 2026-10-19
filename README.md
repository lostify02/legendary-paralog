# legendary-paralog
from asyncio import EventLoop
from turtle import speed

import pygame
import random
import sys

pygame.init()

SCREEN_WIDTH = 1000
SCREEN_HEIGHT = 600
FPS = 90

WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GRAY = (128, 128, 128)
GROUND_COLOR = (50, 50, 50)

screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("python dino runner")
clock = pygame.time.Clock()
font = pygame.font.SysFont("Courier", 20, bold=True)

class Dino:
    def __init__(self):
        self.x = 80  
        self.y = 280
        self.width = 40
        self.height = 50
        self.normal_height = 50
        self.duck_height = 25
        self.vel_y = 0
        self.gravity = 0.8
        self.jump_strength = -14
        self.is_jumping = False
        self.is_ducking = False

    def update(self):
        if self.is_jumping:
            self.vel_y += self.gravity
            self.y += self.vel_y

            current_height = self.duck_height if self.is_ducking else self.normal_height
            if self.y >= 330 - current_height:
                self.y = 330 - current_height
                self.is_jumping = False
                self.vel_y = 0 

    def jump(self):
        if not self.is_jumping:
            self.vel_y = self.jump_strength 
            self.is_jumping = True 

    def duck(self, ducking):
        if ducking and not self.is_ducking:
            self.is_ducking = True
            self.height = self.duck_height
            self.y += (self.normal_height - self.duck_height)
        elif not ducking and self.is_ducking:
            self.is_ducking = False 
            self.y -= (self.normal_height - self.duck_height)
            self.height = self.normal_height

    def draw(self, surface):
        pygame.draw.rect(surface, BLACK, (self.x, self.y, self.width, self.height))
        eye_y =  self.y + 6 if not self.is_ducking else self.y + 4
        pygame.draw.rect(surface, WHITE, (self.x + 28, eye_y, 5, 5 ))

    def get_rect(self):
        return pygame.Rect(self.x, self.y, self.width, self.height)

class cactus:
    def __init__(self, speed):
        self.width = random.choice([20, 30, 45])
        self.height = random.choice([40, 60])
        self.x = SCREEN_WIDTH + random.randint(0, 100)
        self.y = 330 - self.height
        self.speed = speed 

    def update(self):
        self.x -= self.speed 

    def draw(self, surface):
        pygame.draw.Rect(surface, GRAY, (self.x, self.y, self.width, self.height))

    def get_rect(self):
        return pygame.Rect(self.x, self.y, self.width, self.height)

class Bird:
    def __init__(self, speed):
        self.width = 35
        self.height = 25
        self.x = SCREEN_WIDTH + random.randint(100, 300)
        self.y = random.choice([240,280])
        self.speed = speed + 2     

    def update(self):
        self.x -= self.speed

    def draw(self, surface):
        pygame.draw.rect(surface, BLACK, (self.x, self.y, self.width, self.height))

    def get_rect(self):
        return pygame.Rect(self.x, self.y, self.width, self.height)

def main():
    din0 = Dino()
    obstacles = []
    game_speed = 7
    score = 0
    high_score = 0
    game_over = False

    spawn_timer = 0

    while True:
        clock.tick(FPS)
        screen.fill(WHITE)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

            if event.type == pygame.KEYDOWN:
                if event.Key == pygame.K_SPACE or event.key == pygame.K_UP:
                    if game_over:
                        dino = Dino()
                        obstacles.clear()
                        game_speed = 7 
                        score = 0 
                        game_over = False 
                    else:
                        dino.jump()

                if event.key == pygame.K_DOWN:
                    dino.duck(True)

            if event.type == pygame.KEYUP:
                if event.key == pygame.K_DOWN:
                    dino.duck(False)

        if not game_over:
            score += 1
            if score > high_score:
                high_score = score 

            if score % 200 == 0:
                game_speed += 0.5

            spawn_timer += 1
            if spawn_timer > random.randint(50, 90):
                if random.random() < 0.25 and score > 300:
                    obstacles.append(Bird(game_speed))
                else:
                    obstacles.append(cactus(game_speed))
                spawm_timer = 0 

            dino.update()
                                
                            
