# PHP TokenGenerator

Una clase PHP robusta para generar tokens seguros con múltiples opciones de configuración, validación y gestión de caducidad.

## Características

- Generación de tokens aleatorios criptográficamente seguros
- Múltiples formatos: alfanuméricos, hexadecimales, con caracteres especiales
- Tokens basados en hash
- Soporte para tiempo de expiración
- Implementación básica de JWT (JSON Web Tokens)
- Validación de tokens
- Diseño fluido con encadenamiento de métodos (method chaining)

## Requisitos

- PHP 7.4 o superior
- Extensión OpenSSL habilitada

## Instalación

1. Descarga los archivos `TokenGenerator.php` y colócalo en tu proyecto
2. Incluye la clase en tu código con `require_once 'TokenGenerator.php'`

## Ejemplos de uso

### Ejemplo básico

```php
// Incluir la clase TokenGenerator
require_once 'TokenGenerator.php';

// Crear instancia con configuración predeterminada
$tokenGenerator = new TokenGenerator();

// Generar un token simple
$token = $tokenGenerator->generate();
echo "Token simple: " . $token;
// Resultado: Token simple: 8f3j5h2k9l7m4n6p0q8r3s5t7u9v1w3y
```

### Personalizar longitud y prefijo

```php
// Crear instancia con longitud 16 y prefijo "API_"
$tokenGenerator = new TokenGenerator(16, null, 'API_');
$token = $tokenGenerator->generate();
echo "Token personalizado: " . $token;
// Resultado: Token personalizado: API_4f8h2j5k9l3m7n0
```

### Usar diferentes conjuntos de caracteres

```php
$tokenGenerator = new TokenGenerator(12);

// Generar token hexadecimal
$hexToken = $tokenGenerator->useHexadecimal()->generate();
echo "Token hexadecimal: " . $hexToken;
// Resultado: Token hexadecimal: 3a7b2c1d4e9f

// Generar token con caracteres especiales
$specialToken = $tokenGenerator->useSpecialChars()->generate();
echo "Token con caracteres especiales: " . $specialToken;
// Resultado: Token con caracteres especiales: a5!b@9#c$7%d
```

### Generar token como hash

```php
$tokenGenerator = new TokenGenerator();
$hashToken = $tokenGenerator->generateHash('usuario123');
echo "Token hash: " . $hashToken;
// Resultado: Token hash: 8f7e6d5c4b3a2f1e0d9c8b7a6f5e4d3c2b1a0f9e8d7c6b5a4f3e2d1c0
```

### Generar token con tiempo de caducidad

```php
// Crear token que expira en 1 hora (3600 segundos)
$tokenGenerator = new TokenGenerator(32, 3600);
$tokenData = $tokenGenerator->generateWithExpiry(['user_id' => 123]);

echo "Token con caducidad: " . $tokenData['token'] . "\n";
echo "Expira en: " . date('Y-m-d H:i:s', $tokenData['expires_at']) . "\n";

// Verificar si el token ha expirado
if (TokenGenerator::isValid($tokenData['expires_at'])) {
    echo "El token sigue siendo válido";
} else {
    echo "El token ha expirado";
}
```

### Generar y verificar un JWT

```php
$tokenGenerator = new TokenGenerator();
$secret = 'mi_clave_secreta_muy_segura';

// Generar JWT con payload personalizado
$jwt = $tokenGenerator->generateJWT([
    'user_id' => 123, 
    'role' => 'admin'
], $secret);

echo "JWT: " . $jwt . "\n";

// Verificar el JWT
$payload = TokenGenerator::verifyJWT($jwt, $secret);
if ($payload) {
    echo "JWT verificado correctamente. Payload: " . json_encode($payload);
} else {
    echo "JWT inválido o expirado";
}
```

### Uso con encadenamiento de métodos

```php
$token = (new TokenGenerator())
    ->setLength(20)
    ->setPrefix('SECURE_')
    ->useAlphanumeric()
    ->generate();

echo "Token con encadenamiento: " . $token;
// Resultado: Token con encadenamiento: SECURE_j5k8l3m9n4p2q7r6s1t
```

## Método de generación JWT

La clase proporciona una implementación básica de JWT que puede ser útil para casos simples. Sin embargo, para entornos de producción con requisitos avanzados, considera usar bibliotecas especializadas como [firebase/php-jwt](https://github.com/firebase/php-jwt).

## Métodos disponibles

| Método | Descripción |
|--------|-------------|
| `__construct(?int $length = null, ?int $expiry = null, ?string $prefix = null)` | Constructor con parámetros opcionales |
| `setLength(int $length): self` | Establece la longitud del token (mínimo 8) |
| `setExpiry(int $seconds): self` | Establece el tiempo de caducidad en segundos |
| `setPrefix(?string $prefix): self` | Establece un prefijo para el token |
| `setCharset(string $charset): self` | Personaliza los caracteres permitidos |
| `useAlphanumeric(): self` | Usa solo caracteres alfanuméricos |
| `useHexadecimal(): self` | Usa solo caracteres hexadecimales |
| `useSpecialChars(): self` | Usa caracteres alfanuméricos y especiales |
| `generate(): string` | Genera un token aleatorio |
| `generateHash(string $data = ''): string` | Genera un token basado en hash |
| `generateWithExpiry(array $payload = []): array` | Genera token con información de caducidad |
| `isValid(int $expiryTimestamp): bool` | Verifica si un token sigue siendo válido |
| `generateJWT(array $payload, string $secret): string` | Genera un JWT simple |
| `verifyJWT(string $jwt, string $secret)` | Verifica un JWT y devuelve su payload |

## Consideraciones de seguridad

- La clase utiliza funciones criptográficamente seguras como `random_int()` y `random_bytes()`
- La longitud mínima del token es de 8 caracteres para garantizar seguridad
- Se recomienda usar una longitud de al menos 32 caracteres para tokens de alta seguridad
- Para JWT en entornos de producción, considera usar bibliotecas especializadas

## Licencia

Este código se proporciona bajo licencia MIT.
