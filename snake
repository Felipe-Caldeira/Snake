# Imports
import pygame
from pygame.locals import *
import random
import os


# Global variables

os.environ['SDL_VIDEO_CENTERED'] = '1'

main_dir = os.path.split(os.path.abspath(__file__))[0]
data_dir = os.path.join(main_dir, 'data')

BLACK = (0, 0, 0)
GRAY = (211, 211, 211)
DARK_GRAY = (200, 200, 200)
WHITE = (255, 255, 255)
DARK = (100, 100, 100)
LIGHT_BLUE = (135, 206, 250)
BLUE = (35, 165, 246)

SCREEN_WIDTH, SCREEN_HEIGHT = 800, 800

# Graphics classes and functions


class TextObj(pygame.font.Font):
    def __init__(self, surface, text, x, y, font=None, size=32, pos='centered'):
        pygame.font.Font.__init__(self, font, size)
        self.SurfObj = self.render(text, True, (0, 0, 0,))
        self.RectObj = self.SurfObj.get_rect()
        if pos == 'centered':
            self.RectObj.center = (x, y)
        if pos == 'top_left':
            self.RectObj = (x, y)
        surface.blit(self.SurfObj, self.RectObj)


class Button:
    def __init__(self, surf_dest, text, x, y, width=250, height=50, value=None, color1=GRAY, color2=DARK_GRAY):
        self.surf_dest = surf_dest
        self.color1 = color1
        self.color2 = color2

        self.Surf = pygame.Surface((width, height))
        self.Rect = self.Surf.get_rect()
        self.Rect.center = (x, y)
        self.Surf.fill(self.color1)

        self.text = TextObj(self.Surf, text, width/2, height/2)
        self.Surf.blit(self.text.SurfObj, self.text.RectObj)
        self.surf_dest.blit(self.Surf, self.Rect)

        self.value = value

    def hovered(self, target):
        return target.collidepoint(pygame.mouse.get_pos())

    def update(self):
        if self.hovered(self.Rect):
            self.darken()
        else:
            self.lighten()

    def darken(self):
        self.Surf.fill(self.color2)
        self.Surf.blit(self.text.SurfObj, self.text.RectObj)
        self.surf_dest.blit(self.Surf, self.Rect)

    def lighten(self):
        self.Surf.fill(self.color1)
        self.Surf.blit(self.text.SurfObj, self.text.RectObj)
        self.surf_dest.blit(self.Surf, self.Rect)

    def clicked(self):
        if self.hovered(self.Rect):
            return True


def load_image(name, width, height, colorkey=None):
    fullname = os.path.join(data_dir, name)
    image = None
    try:
        image = pygame.image.load(fullname)
    except pygame.error:
        print('Image not found: ' + fullname)
    image = image.convert()
    image = pygame.transform.scale(image, (width, height))

    if colorkey is not None:
        if colorkey is -1:
            colorkey = image.get_at((0, 0))
            image.set_colorkey(colorkey, RLEACCEL)
    return image, image.get_rect()


def play_music(name, loop):
    fullname = os.path.join(data_dir, name)
    pygame.mixer.music.load(fullname)
    pygame.mixer.music.play(loop)


# Game classes and functions

class Player(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image, self.rect = load_image('snake_head.png', 50, 50, -1)
        self.rect.midbottom = 250, 250

        self.direction = 'right'
        self.movespeed = 4

        self.x, self.y = self.rect.midbottom
        self.Vx = 0
        self.Vy = 0

    def update(self):
        self.x, self.y = self.rect.midbottom

        self.pressed_key_events()

        self.gravity()

    def pressed_key_events(self):
        pressed = pygame.key.get_pressed()

        if pressed[pygame.K_w]:
            pass
        elif pressed[pygame.K_s]:
            pass
        elif pressed[pygame.K_d]:
            self.turn('right')
            self.move()
        elif pressed[pygame.K_a]:
            self.turn('left')
            self.move()
        if pressed[pygame.K_SPACE]:
            self.jump()
        if not pressed[pygame.K_a] and not pressed[pygame.K_d]:
            self.Vx = 0

    def move(self):
        if self.Vx < 5:
            self.Vx += 1
        if self.direction == 'right':
            self.rect.move_ip((self.Vx, 0))
        elif self.direction == 'left':
            self.rect.move_ip((-self.Vx, 0))

    def turn(self, new_direction):
        if self.direction == 'right' and new_direction == 'left':
            self.direction = new_direction
            pygame.transform.flip(self.image,True, False)
        elif self.direction == 'left' and new_direction == 'right':
            self.direction = new_direction
            pygame.transform.flip(self.image, True, False)

    def jump(self):
        if self.y == SCREEN_HEIGHT:
            self.Vy = -20
            self.rect.move_ip((0, self.Vy))

    def gravity(self):
        if self.y < SCREEN_HEIGHT:
            self.Vy += 1
            self.rect.move_ip((0, self.Vy))
            if self.rect.bottom > SCREEN_HEIGHT:
                self.rect.bottom = SCREEN_HEIGHT



# ----------------------------------------------------------------------

# Initialize

pygame.init()
window = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption('TestCraft!')

# Game variables

restart = False

# Clocks and timers

clock = pygame.time.Clock()

# Prepare sprite groups

playersprite = pygame.sprite.GroupSingle()

# Main function

def main():

# Create main menu

    menuscreen = pygame.Surface((SCREEN_HEIGHT, SCREEN_WIDTH))
    menuscreen.fill(DARK)

    menu_text = TextObj(menuscreen, "TESTCRAFT!", menuscreen.get_width() / 2, menuscreen.get_height() / 6, size=50)
    start = Button(menuscreen, "Start game!", menuscreen.get_width() / 2, menuscreen.get_height() / 2)

# Create game screen and background

    gamescreen = pygame.Surface((SCREEN_WIDTH, SCREEN_HEIGHT))
    gamescreen.fill(DARK_GRAY)

# Menu loop

    menu_open = True
    while menu_open:
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()
                exit()
            elif event.type == MOUSEMOTION:
                start.update()
            elif event.type == MOUSEBUTTONDOWN:
                if start.clicked():
                    menu_open = False

        window.blit(menuscreen, (0, 0))
        pygame.display.update()

# Prepare game objects and spritegroups

    player = Player()

    playersprite.add(player)

# Main loop

    game_run = True
    while game_run:
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()
                exit()

        playersprite.update()

        gamescreen.fill(DARK_GRAY)
        playersprite.draw(gamescreen)
        window.blit(gamescreen, (0, 0))
        pygame.display.update()

# Game over loop

    game_over = TextObj(gamescreen, "GAME OVER!", SCREEN_WIDTH/2, SCREEN_HEIGHT/2 - 100, size=100)
    play_again = Button(gamescreen, "Play again", SCREEN_WIDTH/2, SCREEN_HEIGHT/2, color1=LIGHT_BLUE, color2=BLUE)

    global restart
    restart = False
    while True:
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == QUIT:
                pygame.quit()
                exit()
            elif event.type == MOUSEMOTION:
                play_again.update()
            elif event.type == MOUSEBUTTONDOWN:
                if play_again.clicked():
                    restart = True
        if restart:
            break
        window.blit(gamescreen, (0, 0))
        pygame.display.update()

# Call main on init

if __name__ == '__main__':
    main()

while True:
    if restart:
        main()
