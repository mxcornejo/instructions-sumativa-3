Usuario Contraseña (pruebas) Rol
admin admin123 ROLE_ADMIN
chef chef123 ROLE_USER
usuario user123

cd backend

export APP_JWT_SECRET="mi-secreto-super-seguro-de-32-chars-min"
export APP_ADMIN_PASSWORD="Admin123!"
export APP_CHEF_PASSWORD="Chef123!"
export APP_USER_PASSWORD="User123!"

./mvnw spring-boot:run

docker:

# Solo el frontend

docker compose up --build frontend

# Solo el backend

docker compose up --build backend
