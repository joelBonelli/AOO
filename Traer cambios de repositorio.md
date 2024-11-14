## Traer cambios de repositorio 

1. **Agregar el repositorio original como un "remote" llamado `upstream`** (solo deben hacer esto una vez):
   ```bash
   git remote add upstream <URL-del-repositorio-original>
   ```
   Sustituye `<URL-del-repositorio-original>` con la URL de tu repositorio.

2. **Traer los cambios del repositorio original**:
   ```bash
   git fetch upstream
   ```

3. **Fusionar los cambios del branch principal de `upstream`** en su propio branch:
   ```bash
   git merge upstream/main
   ```
   (o `upstream/master`, si el branch principal se llama `master` en lugar de `main`).

4. **Resolver cualquier conflicto** que pueda surgir durante la fusión. Si se presentan conflictos, ellos deberán resolverlos manualmente, luego hacer `git add` de los archivos resueltos y completar la fusión con:
   ```bash
   git commit
   ```

5. **Actualizar su repositorio en GitHub** (si desean que los cambios también se reflejen en su fork en GitHub):
   ```bash
   git push origin <nombre-de-su-branch>
   ```

Con estos comandos, tus alumnos podrán mantener sus forks sincronizados con los últimos cambios de tu repositorio original.
