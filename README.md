# FunshiOS

FunshiOS es un sistema operativo de 64 bits desarrollado desde cero. El objetivo central del proyecto es la portabilidad estricta entre arquitecturas heterogéneas, permitiendo la ejecución del mismo sistema tanto en computadoras de placa única (ARM64 como Raspberry Pi) como en hardware de escritorio estándar (x86_64 como AMD Ryzen o Intel Core).

---

## Arquitectura y Diseño

El sistema rechaza el modelo monolítico tradicional para evitar el acoplamiento directo al hardware, estructurándose bajo dos principios:

*   **Micronúcleo (Microkernel):** El kernel se ejecuta con privilegios mínimos (Ring 0) y delega la mayor parte de las tareas del sistema al espacio de usuario (User Space / Ring 3). Sus responsabilidades se limitan a la gestión de memoria física elemental, la planificación de hilos (scheduling) y la comunicación entre procesos (IPC). Los controladores de hardware, sistemas de archivos y pilas de red corren como servidores aislados, previniendo que un fallo en un componente colapse el sistema completo.
*   **Abstracción de Hardware (HAL):** La lógica de alto nivel está completamente desacoplada del silicio. La arquitectura divide el código específico de la CPU en módulos independientes (`/arch`), aislando las diferencias de inicialización y manejo de interrupciones entre ARM y x86_64 sin alterar el resto del sistema de archivos o servidores.

---

## Especificaciones Técnicas

*   **Lenguaje:** C y Ensamblador (Assembly).
*   **Modo de ejecución:** Nativo de 64 bits (Long Mode).
*   **Arranque:** UEFI nativo, omitiendo el soporte para BIOS heredada (Legacy) y obteniendo un mapa de memoria limpio desde el inicio.
*   **Fase actual:** Desarrollo de la base del micronúcleo y la HAL para la arquitectura objetivo inicial (`x86_64`). Una vez estable, se iniciará la compilación cruzada hacia `ARM64`.

---

## Toolchain y Entorno de Desarrollo

El entorno de compilación está aislado de las librerías del sistema anfitrión (host) mediante el siguiente conjunto de herramientas:

*   **Compilador:** `x86_64-elf-gcc` y `binutils` como compilador cruzado independiente.
*   **Emulación:** QEMU para la simulación del entorno x86_64, incluyendo soporte para multiprocesamiento simétrico (SMP).
*   **Depuración:** GDB conectado al socket de depuración remota de QEMU para inspección de registros y traza de ejecución.

---

## Estructura del Repositorio

```text
funshios/
├── .github/             # Automatizaciones y configuraciones de GitHub
├── boot/                # Cargador de arranque (Bootloader y archivos UEFI)
├── kernel/              # Código fuente del Micronúcleo
│   ├── arch/            # Capa de Abstracción de Hardware (HAL)
│   │   ├── x86_64/      # GDT, IDT, paginación y controladores específicos de PC
│   │   └── arm64/       # (Planificado) Inicialización para ARM
│   ├── sys/             # Lógica core (IPC, Scheduler, gestión de memoria)
│   └── include/         # Cabeceras (.h) globales del kernel
├── servers/             # Componentes en espacio de usuario (Drivers, VFS)
└── Makefile             # Automatización del ciclo de compilación y pruebas
```

---

## Contribuciones

El proyecto se encuentra en una etapa temprana de desarrollo. Los reportes de errores, discusiones de arquitectura y Pull Requests son bienvenidos a través del sistema de Issues del repositorio.
