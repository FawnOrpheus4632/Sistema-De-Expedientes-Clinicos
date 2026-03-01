### RNF - 01 Seguridad del mecanismo de autenticación por código
**Requisito:** Autenticación mediante código de un solo uso (OTP).

**Descripción:**
El sistema deberá diseñarse para utilizar códigos de acceso de un solo uso enviados al correo electrónico registrado del terapeuta o administrador como mecanismo de autenticación temporal.

**Restricciones:**
- El código deberá tener una vigencia limitada (5–10 minutos).
- El código solo podrá utilizarse una vez.
- El diseño no deberá permitir reutilización del código.

**Criterios de aceptación:**
- El sistema genera códigos OTP con longitud de 6 caracteres.
- El sistema invalida automáticamente cualquier código marcado como utilizado con anterioridad.
- El código no se almacena en texto plano, sino mediante una función hash criptográfica.

### RNF - 02 Protección contra accesos no autorizados
**Requisito:** Control de intentos fallidos.

**Descripción:**
El sistema deberá implementar un mecanismo de limitación de intentos fallidos al ingresar el código enviado por correo, para prevenir ataques de fuerza bruta.

**Restricciones:**
- No se permitirán intentos ilimitados de validación del código.
- El diseño debe implementar un bloqueo temporal tras cierto número de intentos fallidos.

**Criterios de aceptación:**
- El sistema permite hasta un maximo de 5 intentos fallidos por codigo.
- El código es marcado como bloqueado al superar el número máximo de intentos.
- El sistema aplica un bloqueo temporal mínimo de 10 minutos tras superar el límite de intentos.

### RNF - 03 Conservación de expedientes clínicos
**Requisito:** Preservación en lugar de eliminación física de pacientes

**Descripción:**
El sistema deberá diseñarse para que la "eliminación" de pacientes no implique la eliminación completa de sus datos en la base de datos, sino su archivado, preservando el expediente clínico.

**Restricciones:**

- No se permitirá la eliminación permanente de expedientes clínicos desde la interfaz del sistema.
- El diseño deberá tener un atributo de estado (activo/archivado).
- Los pacientes archivados no deberán aparecer en listados activos.
- La información archivada no podrá ser modificada.

**Criterios de aceptación:**
- El sistema muestra un atributo de estado para el expediente de cada paciente.
- El sistema impide la modificación de la información en el expediente del paciente una vez este ha sido archivado.
