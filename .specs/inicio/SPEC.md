# .specs/inicio/SPEC.md — Landing Page: "El Impacto de la IA" (React + Terraform)

## 1. Objetivo y Visión
* **Qué:** Construir y desplegar una aplicación de página única (SPA) estática y de alto rendimiento que describa visualmente los beneficios y el poder transformador de la Inteligencia Artificial.
* **Por qué:** Servir como una landing page altamente disponible, segura y rentable que demuestre una arquitectura web moderna (React + Vite) orquestada mediante Infraestructura como Código (Terraform).
* **Audiencia Objetivo:** Partes interesadas técnicas y no técnicas que buscan comprender los beneficios clave de la IA.

## 2. Stack Tecnológico y Versiones
* **Frontend:** React 18+, TypeScript, Vite (Herramienta de compilación), Tailwind CSS (Estilos).
* **Infraestructura:** Terraform v1.5+, AWS Provider v5.0+.
* **Servicios de AWS:** Amazon S3 (Alojamiento estático), Amazon CloudFront (CDN + Seguridad mediante OAC).

## 3. Comandos
*El Agente de IA debe utilizar y probar la conformidad del código usando exactamente los siguientes comandos:*
* **Configuración del Frontend:** `cd frontend && npm install`
* **Compilación del Frontend:** `npm run build` *(Genera los assets listos para producción en `frontend/dist/`)*
* **Linting del Frontend:** `npm run lint --fix`
* **Inicialización de Terraform:** `cd terraform && terraform init`
* **Validación de Terraform:** `terraform validate`
* **Planificación de Terraform:** `terraform plan -out=tfplan`
* **Aplicación de Terraform:** `terraform apply tfplan`

## 4. Estructura del Proyecto
*El Agente de IA debe generar y organizar los archivos estrictamente de acuerdo con este diseño. No crear directorios de nivel superior fuera de este esquema:*
````text
├── .specs/
│   └── inicio/
│       └── SPEC.md            # Este archivo de especificación (Fuente de verdad)
├── README.md                  # Guía de ejecución
├── frontend/                  # Aplicación React + Vite
│   ├── src/
│   │   ├── components/        # Hero, Features, Testimonials
│   │   ├── App.tsx
│   │   └── main.tsx
│   ├── index.html
│   ├── package.json
│   ├── vite.config.ts
│   └── tailwind.config.js
└── terraform/                 # Infraestructura como Código
    ├── main.tf                # Bloque de Terraform y Proveedores (AWS)
    ├── s3.tf                  # Definiciones del Bucket S3 Privado
    ├── cloudfront.tf          # Distribución de CloudFront y Origin Access Control (OAC)
    ├── variables.tf           # Variables de entrada fuertemente tipadas
    └── outputs.tf             # Variables de salida (URL de CloudFront, ID del Bucket S3)
````

## 5. Estilo de Código y Fragmentos de Referencia
El Agente de IA debe adherirse a estos estándares de código y patrones de diseño:

* **React**: Usar componentes funcionales con interfaces TypeScript para las props. Evitar estilos en línea; usar clases de utilidad semánticas de Tailwind CSS.

* **Terraform**: Seguir las convenciones de nomenclatura estándar (snake_case). Agrupar los recursos relacionados en archivos separados como se especifica en la estructura. Usar siempre argumentos de recursos explícitos en lugar de valores por defecto cuando se trate de seguridad.

### Fragmento de Referencia: Integración Segura de CloudFront OAC

Asegurar que el bucket S3 bloquee estrictamente el acceso público y permita la entrada ÚNICAMENTE a través de CloudFront OAC:
````terraform
# Patrón de ejemplo para cloudfront.tf
resource "aws_cloudfront_origin_access_control" "oac" {
  name                              = "s3-oac-${var.project_name}"
  description                       = "OAC para el sitio web estático seguro en S3"
  origin_access_control_origin_type = "s3"
  signing_behavior                  = "always"
  signing_protocol                  = "sigv4"
}
````

## 6. Flujo de Trabajo de Git (Git Workflow)
* **Estrategia de Ramas**: Trabajar en las características en ramas aisladas (por ejemplo, `feature/frontend-ui`, `feature/tf-cloudfront`).
* **Pautas de Commit**: Usar el estándar de Conventional Commits (por ejemplo, `feat(frontend): add hero section`, `feat(infra): configure cloudfront distribution with oac`).

## 7. Límites y Restricciones (Crucial)
* **🚫 NUNCA** exponer permisos de lectura pública en el bucket S3 de forma nativa (el bloque `website` o las políticas públicas están prohibidos). Usar exclusivamente CloudFront OAC.
* **🚫 NUNCA** dejar credenciales de AWS hardcodeadas (`access_key`, `secret_key`) dentro de main.tf. Usar variables de entorno o perfiles locales de la CLI de AWS.
* **🚫 NUNCA** modificar o agregar archivos dentro de `frontend/dist/` manualmente. Todos los archivos estáticos deben ser generados puramente por el motor de compilación de Vite.
* **⚠️ PREGUNTAR PRIMERO** antes de agregar cualquier paquete npm externo para componentes de UI (por ejemplo, Framer Motion o Lucide Icons). Preferir primitivas nativas de Tailwind a menos que se autorice lo contrario.

## 8. Criterios de Éxito (Definición de Hecho / DoD)
1. **Cero Errores**: `npm run build` y `terraform validate` se ejecutan con un código de salida 0.
2. **Verificación de Seguridad**: El Bucket S3 tiene `aws_s3_bucket_public_access_block` habilitado y configurado en `true` en los 4 parámetros.
3. **Protocolo de Enrutamiento SPA**: La distribución de CloudFront incluye respuestas de error personalizadas que redirigen los errores `403` y `404` de vuelta a `/index.html` con un código de estado `200 OK`.
4. **Verificación de Salida**: Ejecutar `terraform apply` devuelve con éxito `cloudfront_domain_name` apuntando al frontend de React desplegado.