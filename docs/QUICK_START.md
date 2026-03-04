# Quick Start - Autenticación de gog

## Para usuarios que ya tienen el setup básico

### Paso 1: Copiar credenciales

```powershell
docker cp "client_secret_*.json" regiclaw-openclaw-gateway-1:/tmp/gog-config/credentials.json
docker-compose exec openclaw-gateway bash -c 'export HOME=/tmp && gog auth credentials set /tmp/gog-config/credentials.json'
```

### Paso 2: Autenticar (Parte 1)

```powershell
docker-compose exec openclaw-gateway bash -c 'export HOME=/tmp && export GOG_KEYRING_PASSWORD=insecure && gog auth add TU-EMAIL@gmail.com --services gmail,calendar,drive,contacts,docs,sheets --remote --step 1 --json' | ConvertFrom-Json | Select-Object -ExpandProperty auth_url | Out-File -FilePath auth_url.txt -Encoding ASCII -NoNewline

Start-Process (Get-Content auth_url.txt)
```

### Paso 3: Autorizar en navegador

1. Autoriza en Google (acepta permisos)
2. Copia la URL completa del callback (aunque diga "no se puede conectar")

### Paso 4: Autenticar (Parte 2)

```powershell
@"
export HOME=/tmp
export GOG_KEYRING_PASSWORD=insecure
gog auth add TU-EMAIL@gmail.com --services gmail,calendar,drive,contacts,docs,sheets --remote --step 2 --auth-url "PEGA_URL_DEL_CALLBACK_AQUI"
"@ | Out-File -FilePath tmp_auth_step2.sh -Encoding ASCII

Get-Content tmp_auth_step2.sh | docker exec -i regiclaw-openclaw-gateway-1 bash
Remove-Item tmp_auth_step2.sh, auth_url.txt
```

### Paso 5: Verificar

```powershell
docker-compose exec openclaw-gateway sh -c 'export HOME=/tmp && gog auth list'
docker-compose exec openclaw-gateway sh -c 'export HOME=/tmp && gog drive ls --account TU-EMAIL@gmail.com'
```

✅ Si ves tu cuenta listada, ¡estás listo!

---

**Para detalles completos, ver:** [GOG_AUTHENTICATION_GUIDE.md](./GOG_AUTHENTICATION_GUIDE.md)
