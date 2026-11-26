# Evidencia: Ciclo de Vida de un Usuario IAM

**Estudiante:** Camilo Ramírez  
**Actividad:** Ciclo de vida de un usuario IAM (Semana 1)

---

## Salidas de Comandos Solicitadas

> **Nota de seguridad:** No incluir claves secretas (`SecretAccessKey`) en este documento.

### Paso 3: Datos iniciales del usuario (`Arn`)
Comando: `aws iam get-user --user-name jose`
```json
{
    "User": {
        "Path": "/",
        "UserName": "jose",
        "UserId": "*********************",
        "Arn": "arn:aws:iam::847547470714:user/jose",
        "CreateDate": "2026-11-25T20:10:57+00:00"
    }
}
```

---

### Paso 6: Verificación de cambio de nombre y etiquetas
Comando: `aws iam get-user --user-name lector-jose`
```json
{
    "User": {
        "Path": "/",
        "UserName": "lector-jose",
        "UserId": "*********************",
        "Arn": "arn:aws:iam::847547470714:user/lector-jose",
        "CreateDate": "2026-11-25T20:10:57+00:00",
        "Tags": [
            {
                "Key": "area",
                "Value": "desarrollo"
            }
        ]
    }
}
```

---

### Paso 8: Políticas adjuntas al usuario
Comando: `aws iam list-attached-user-policies --user-name lector-jose`
```json
{
    "AttachedPolicies": [
        {
            "PolicyName": "AmazonEC2ReadOnlyAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess"
        }
    ]
}
```

---

### Paso 12: Acción permitida (Lectura EC2 con perfil lector)
Comando: `aws ec2 describe-instances --profile lector`
```json
{
    "Reservations": []
}
```

---

### Paso 13: Acción no permitida (Menor privilegio)
Comando: `aws iam list-users --profile lector`
```text
aws: [ERROR]: An error occurred (AccessDenied) 
```

---

### Paso 15: Acción tras revocar permisos
Comando: `aws ec2 describe-instances --profile lector`
```text
aws: [ERROR]: An error occurred (UnauthorizedOperation) when calling the DescribeInstances operation: You are not authorized to perform this operation. User: arn:aws:iam::847547470714:user/lector-jose is not authorized to perform: ec2:DescribeInstances because no identity-based policy allows the ec2:DescribeInstances action
```

---

### Paso 19: Confirmación de eliminación del usuario
Comando: `aws iam list-users --query "Users[].UserName" --output table`
```text
------------------
|    ListUsers   |
+----------------+
|  admin-camilo  |
+----------------+
```

---

## Preguntas de Reflexión

### 1. ¿Qué podía hacer el usuario justo después de crearlo, antes de adjuntarle una política? ¿Por qué?
> **Respuesta:**  
> *nada. Un usuario IAM recién creado no puede hacer ninguna acción, ni siquiera ver recursos o listar usuarios.*

### 2. En el error del paso 13, ¿qué tres datos te dice el mensaje?
> **Respuesta:**  
> *Quién intentó la acción, qué acción se intentó y Por qué falló*

### 3. ¿Por qué cambió el `Arn` al renombrar el usuario?
> **Respuesta:**  
> *porque el ARN incluye el nombre del usuario. Al cambiar el nombre, el ARN cambia con él.*

### 4. ¿Por qué AWS obliga a borrar las llaves antes de borrar el usuario?
> **Respuesta:**  
> *porque IAM no deja borrar algo que todavía tiene elementos asociados. Si lo permitiera, quedarían llaves "huérfanas" apuntando a un usuario que ya no existe.*

### 5. ¿Qué pasaría si le hubieras dado `AdministratorAccess` en vez de solo lectura? ¿En qué caso real estaría justificado?
> **Respuesta:**  
> *Tendría control total sobre todos los recursos y servicios de la cuenta*
