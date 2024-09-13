import pygame
import random
import sys

# Inicializar Pygame
pygame.init()

# Configuración de la pantalla
ANCHO, ALTO = 800, 600
pantalla = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Combate de Caballeros")

# Colores
BLANCO = (255, 255, 255)
NEGRO = (0, 0, 0)
ROJO = (255, 0, 0)
AZUL = (0, 0, 255)

# Clase Caballero
class Caballero:
    def __init__(self, nombre, constelacion, vida, ataque, defensa, habilidades, color, x, y):
        self.nombre = nombre
        self.constelacion = constelacion
        self.vida = vida
        self.ataque = ataque
        self.defensa = defensa
        self.habilidades = habilidades
        self.imagen = pygame.Surface((100, 100))
        self.imagen.fill(color)
        self.rect = self.imagen.get_rect(topleft=(x, y))
    
    def usar_habilidad(self, enemigo):
        habilidad = random.choice(list(self.habilidades.keys()))
        daño = self.habilidades[habilidad]
        enemigo.vida -= daño
        return f"{self.nombre} usa {habilidad} y causa {daño} de daño."

    def atacar(self, enemigo):
        daño = self.ataque - enemigo.defensa
        if daño > 0:
            enemigo.vida -= daño
        return f"{self.nombre} ataca a {enemigo.nombre} y causa {daño} de daño."

    def esta_vivo(self):
        return self.vida > 0

    def mover(self, dx, dy):
        self.rect.x += dx
        self.rect.y += dy

    def actualizar(self):
        if self.rect.left < 0:
            self.rect.left = 0
        if self.rect.right > ANCHO:
            self.rect.right = ANCHO
        if self.rect.top < 0:
            self.rect.top = 0
        if self.rect.bottom > ALTO:
            self.rect.bottom = ALTO

# Inicializar caballeros
habilidades_caballero1 = {
    "Meteoros de Pegaso": 20,
    "Puntada Estelar": 15
}
habilidades_caballero2 = {
    "Rugido del Dragón": 25,
    "Llamas del Dragón": 10
}

caballero1 = Caballero("Caballero de Pegaso", "Pegaso", 100, 30, 10, habilidades_caballero1, ROJO, 50, ALTO // 2)
caballero2 = Caballero("Caballero de Dragón", "Dragón", 100, 25, 12, habilidades_caballero2, AZUL, ANCHO - 150, ALTO // 2)

# Función de renderizado
def renderizar():
    pantalla.fill(BLANCO)
    pantalla.blit(caballero1.imagen, caballero1.rect)
    pantalla.blit(caballero2.imagen, caballero2.rect)
    
    fuente = pygame.font.Font(None, 36)
    texto1 = fuente.render(f"{caballero1.nombre}: {caballero1.vida} HP", True, NEGRO)
    texto2 = fuente.render(f"{caballero2.nombre}: {caballero2.vida} HP", True, NEGRO)
    
    pantalla.blit(texto1, (50, 50))
    pantalla.blit(texto2, (ANCHO - 250, 50))

    pygame.display.flip()

# Ciclo principal del juego
def juego():
    reloj = pygame.time.Clock()
    turno = 1
    while caballero1.esta_vivo() and caballero2.esta_vivo():
        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

        teclas = pygame.key.get_pressed()
        if teclas[pygame.K_LEFT]:
            caballero1.mover(-5, 0)
        if teclas[pygame.K_RIGHT]:
            caballero1.mover(5, 0)
        if teclas[pygame.K_UP]:
            caballero1.mover(0, -5)
        if teclas[pygame.K_DOWN]:
            caballero1.mover(0, 5)
        if teclas[pygame.K_SPACE] and caballero1.esta_vivo():
            resultado = caballero1.usar_habilidad(caballero2)
            if caballero2.esta_vivo():
                resultado += "\n" + caballero2.atacar(caballero1)
        
        caballero1.actualizar()
        caballero2.rect.x += (caballero1.rect.x - caballero2.rect.x) * 0.05
        caballero2.rect.y += (caballero1.rect.y - caballero2.rect.y) * 0.05

        renderizar()
        reloj.tick(30)  # 30 FPS

    if caballero1.esta_vivo():
        print(f"\n{caballero1.nombre} ha ganado!")
    else:
        print(f"\n{caballero2.nombre} ha ganado!")

# Iniciar el juego
juego()
pygame.quit()
