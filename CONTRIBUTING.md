# Cómo trabajamos

## Idiomas

- **Código, nombres, commits y ramas:** en inglés.
- **Documentación (README, ADRs, diagramas, backlog):** en español.

## Regla número uno: el repo es público

Todo lo que entra a git queda visible y se puede copiar, aunque después se borre o el repo pase a privado. Por eso nunca se suben:

- Secretos: contraseñas, tokens, claves de API, archivos `.env`.
- Certificados o claves privadas (ARCA, TLS).
- Datos reales de clientes: nombres, CUIT, precios, ventas, copias de bases.

Los secretos van en los secretos de cada environment de GitHub o en la máquina donde corre el servicio. Los datos de prueba se inventan.

Si algo se subió por error, avisá enseguida. Un secreto expuesto se rota, no alcanza con borrar el commit.

## Flujo de trabajo

1. Cada cambio sale de una issue. La rama lleva su clave: `CTJ-232-walking-skeleton`.
2. Commits chicos con [Conventional Commits](https://www.conventionalcommits.org/): `feat:`, `fix:`, `docs:`, `test:`, `refactor:`, `chore:`, `ci:`.
3. Pull request contra `main`, con la plantilla completa.
4. **Nadie mergea su propio PR.** La otra persona revisa y aprueba.
5. CI tiene que estar en verde para mergear.

## Entornos

| Entorno | Se despliega |
|---|---|
| Desarrollo | En la PC de cada uno |
| Pruebas | Solo, al mergear a `main` |
| Producción | Desde un tag `vX.Y.Z`, con aprobación manual |
