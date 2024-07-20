# Chasm-Network Guide

# Resmi dökümana [burada](https://network-docs.chasm.net/chasm-scout-season-0/chasm-inference-scout-setup-guide) ulaşabilirsiniz.
# Discorda [buradan](https://discord.gg/gYKWwmfv) ulaşabilirsiniz.

# Chasm Network'te bir düğüm çalıştırıyoruz ve puan topluyoruz

# Düşük bir sistem ile rahat çalışır

# Biraz (0.1) da Mantle Mainnet ağında Feelik token gerekiyor. 

# -------------- TEST CÜZDANINIZ İLE YAPINIZ ----------------

## Adım 1: Scout Kimliğini ve API Anahtarını Almak için NFT'yi Mintleyin
Bakınız https://scout.chasm.net/private-mint

![Screenshot_100](https://github.com/user-attachments/assets/76c84ff6-656f-4c61-9420-fb345e0a6040)


Buradan `WEBHOOK_API_KEY` ve `SCOUT_UID`yi alacaksınız.

## Adım 2: Groq API Anahtarını Alın
"GROQ_API_KEY'i edinmek" için [Groq](https://console.groq.com/keys) adresinden bir hesaba kaydolun

![Screenshot_99](https://github.com/user-attachments/assets/d7eb8406-cea3-4cec-a66c-006872aca31d)

## Adım 3: İsteğe bağlı: OpenRouter ve OpenAI API anahtarlarını alın
Bu adım isteğe bağlıdır
- [OpenRouter](https://openrouter.ai/) for `OPENROUTER_API_KEY`

- [OpenAI](https://platform.openai.com/api-keys) for `OPENAI_API_KEY`

- OpenAI için telefon numaralarını [buradan](https://smspva.com/?ref=724518) kriptoyla satın alabilirsiniz
  
## Adım 4: Bağımlılıkları Kurun
```console
# Docker'ın resmi GPG anahtarını ekleyin:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Depoyu Apt kaynaklarına ekleyin:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

# Docker'ı yükleyin
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Adım 5: Kurulum
### 5-1: Kök girin
```console
cd ~
```

### 5-2: .env dosyasını oluşturun ve yapılandırın
```console
nano .env
```

Aşağıdaki yapılandırmayı `.env` dosyanıza kopyalayın ve ardından aşağıdaki değişkenleri `=` önünde güncelleyin
- `SCOUT_NAME`: Kendinize bir isim girin
- `SCOUT_UID`: Chasm Sitedeki UID
- `WEBHOOK_API_KEY`: Chasm Sitedeki Keyiniz
- `WEBHOOK_URL`: http://<ip>:3001 adresinizi değiştirin | VPS'nizin IP'si ve bağlantı noktasıyla güncelleyin | Örneğin. http://x.x.x.x:3001/
- `GROQ_API_KEY`: Groq API Anahtarını Alın
- `OPENROUTER_API_KEY`: [Buradan](https://openrouter.ai/settings/keys) kayıt olup API alıp girebilirsiniz.
- `OPENAI_API_KEY`: [Buradan](https://platform.openai.com/api-keys) kayıt olup API alıp girebilirsiniz.
```console
PORT=3001
LOGGER_LEVEL=debug

# Chasm
ORCHESTRATOR_URL=https://orchestrator.chasm.net
SCOUT_NAME=myscout
SCOUT_UID=
WEBHOOK_API_KEY=
WEBHOOK_URL=

# Chosen Provider (groq, openai)
PROVIDERS=groq
MODEL=gemma2-9b-it
GROQ_API_KEY=

# Optional
OPENROUTER_API_KEY=
OPENAI_API_KEY=
```
.env dosyasını kaydedip çıkmak için: `Ctrl + X + Y` , `Enter`

### 5-3: Chasm çalıştırın
```console
# Kodu DockerHub'dan çekin
docker pull johnsonchasm/chasm-scout

# Liman işçisi dosyasını başlat
docker run -d --restart=always --env-file ./.env -p 3001:3001 --name scout johnsonchasm/chasm-scout
```

### 5-4: Doğrulayın
Test Sunucusu Yanıtı:
```console
# Should get "OK" response
curl localhost:3001
```

### 5-5: Yüksek Lisans Sınavı
```console
source ./.env
curl -X POST \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $WEBHOOK_API_KEY" \
     -d '{"body":"{\"model\":\"gemma-7b-it\",\"messages\":[{\"role\":\"system\",\"content\":\"You are a helpful assistant.\"}]}"}' \
     $WEBHOOK_URL
```

### 5-6: Loglara bakın
```console
docker logs scout
```

### Skor tablosunu kontrol edin
https://scout.chasm.net/leaderboard
