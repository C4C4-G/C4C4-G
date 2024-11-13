import pygame
from pygame.locals import *
from OpenGL.GL import *
from OpenGL.GLUT import *
from OpenGL.GLU import *
import math

# 3D Küp için köşe noktaları
vertices = (
    (1, -1, -1),
    (1, 1, -1),
    (-1, 1, -1),
    (-1, -1, -1),
    (1, -1, 1),
    (1, 1, 1),
    (-1, -1, 1),
    (-1, 1, 1)
)

edges = (
    (0, 1),
    (1, 2),
    (2, 3),
    (3, 0),
    (4, 5),
    (5, 6),
    (6, 7),
    (7, 4),
    (0, 4),
    (1, 5),
    (2, 6),
    (3, 7)
)

faces = (
    (0, 1, 2, 3),
    (3, 2, 6, 7),
    (7, 6, 5, 4),
    (4, 5, 1, 0),
    (1, 5, 6, 2),
    (4, 0, 3, 7)
)

colors = (
    (1, 0, 0),
    (0, 1, 0),
    (0, 0, 1),
    (1, 1, 0),
    (1, 0, 1),
    (0, 1, 1)
)

# FPS kontrolü ve kamera ayarları
camera_position = [0, 0, -5]
camera_rotation = [0, 0]
speed = 0.1
sensitivity = 0.2

# 3D küp çizme fonksiyonu
def draw_cube():
    glBegin(GL_QUADS)
    for i, face in enumerate(faces):
        glColor3fv(colors[i])
        for vertex in face:
            glVertex3fv(vertices[vertex])
    glEnd()

    glBegin(GL_LINES)
    for edge in edges:
        for vertex in edge:
            glVertex3fv(vertices[vertex])
    glEnd()

# Ana oyun fonksiyonu
def main():
    pygame.init()
    display = (800, 600)
    pygame.display.set_mode(display, DOUBLEBUF | OPENGL)
    gluPerspective(45, (display[0] / display[1]), 0.1, 50.0)
    glTranslatef(0.0, 0.0, -5)

    clock = pygame.time.Clock()
    angle = 0

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()

        keys = pygame.key.get_pressed()

        # Kamera hareketi (W, A, S, D, Q, E)
        if keys[K_w]:
            camera_position[2] += speed
        if keys[K_s]:
            camera_position[2] -= speed
        if keys[K_a]:
            camera_position[0] += speed
        if keys[K_d]:
            camera_position[0] -= speed
        if keys[K_q]:
            camera_position[1] += speed
        if keys[K_e]:
            camera_position[1] -= speed

        # Fare hareketiyle kamera yönünü değiştirme
        if pygame.mouse.get_pressed()[0]:
            mouse_x, mouse_y = pygame.mouse.get_pos()
            pygame.mouse.set_pos(display[0] // 2, display[1] // 2)
            delta_x = mouse_x - (display[0] // 2)
            delta_y = mouse_y - (display[1] // 2)

            camera_rotation[0] += delta_x * sensitivity
            camera_rotation[1] -= delta_y * sensitivity

        # Kamera dönüşü
        glRotatef(1, camera_rotation[0], camera_rotation[1], 0)

        # Ekranı temizle ve güncelle
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)

        # Küpü çiz
        draw_cube()

        # Kamera konumunu ve yönünü güncelle
        glTranslatef(-camera_position[0], -camera_position[1], -camera_position[2])

        pygame.display.flip()
        clock.tick(60)

# Programı başlat
if __name__ == "__main__":
    main()
