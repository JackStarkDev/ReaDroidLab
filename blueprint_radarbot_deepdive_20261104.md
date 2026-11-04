# 📐 Blueprint Arquitetural — Radarbot (Auditoria de Segurança & Reverse Engineering)

## 1. METADADOS DO APLICATIVO
| Campo | Valor |
|---------|-------|
| **Package** | `com.vialsoft.radarbot_free` |
| **Versão** | `9.35.18` |
| **Min SDK** | 28 (Android 9) |
| **Target SDK** | 36 (Android 16) |
| **Tamanho APK** | 149 MB |
| **Tamanho Descompilado** | 330 MB |
| **Qtde. Atividades** | 83 |
| **Qtde. Services** | 19 |
| **Qtde. Receivers** | 23 |
| **Qtde. Providers** | 7 |
| **Qtde. Permissões** | 45 |

## 2. AUDIT DE SEGURANÇA — PERMISSÕES CRÍTICAS

### ⚠️ PERMISSÕES PERICIOSSAS (Dangerous)
| Permissão | Nível | Uso / Risco |
|-----------|-------|-------------|
| `READ_PHONE_STATE` | Dangerous | Acesso a identidade do dispositivo, número de telefone, estado do SIM. Usado para tracking. |
| `READ_CONTACTS` | Dangerous | Acesso a contatos de usuários. Pode ser usado para envio de dados ou spam. |
| `RECORD_AUDIO` | Dangerous | Gravação de áudio. Pode ser usado para monitoramento de voz. |
| `ACCESS_FINE_LOCATION` | Dangerous | Localização precisa (GPS). Usada para rastreamento de localização. |
| `ACCESS_BACKGROUND_LOCATION` | Dangerous | Localização em segundo plano. Permite rastreamento mesmo quando o app está fechado. |
| `READ_EXTERNAL_STORAGE` | Dangerous (maxSdk=28) | Acesso a arquivos do usuário. Legado (apenas Android ≤12). |
| `SYSTEM_ALERT_WINDOW` | Normal | Overlay sobre outros apps (blindagem visual) |
| `RECEIVE_BOOT_COMPLETED` | Normal | Inicialização após reinício |
| `SCHEDULE_EXACT_ALARM` | Normal | Alarmes precisos (consome bateria) |

### ✅ PERMISSÕES NORMAIS
| Permissão | Nível | Função |
|-----------|-------|--------|
| `INTERNET` | Normal | Conexão com API |
| `ACCESS_FINE_LOCATION` | Normal | Necessário para função principal |
| `FOREGROUND_SERVICE` | Normal | Serviços em primeiro plano |
| `FOREGROUND_SERVICE_LOCATION` | Normal | Serviço de localização |
| `SYSTEM_ALERT_WINDOW` | Normal | Overlay sobre outros apps |
| `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` | Normal | Evitar que o sistema feche o app para economizar bateria |
| `VIBRATE`, `WAKE_LOCK` | Normal | Feedback tátil e manutenção do app ativo |
| `BROADCAST_STICKY` | Normal | Transmissão de intents (não recomendado no Android 8+) |
| `POST_NOTIFICATIONS` | Normal | Notificações |
| `RECEIVE_BOOT_COMPLETED` | Normal | Inicialização após reinício |
| `SCHEDULE_EXACT_ALARM` | Normal | Alarmes precisos (consome bateria) |
| `ACCESS_NETWORK_STATE` | Normal | Verificar estado de rede |
| `ACCESS_WIFI_STATE`, `CHANGE_WIFI_STATE` | Normal | Controle de WiFi |
| `START_ACTIVITIES_FROM_BACKGROUND` | Normal | Iniciar atividades em background |
| `AD_ID` | Normal | Identificador de anúncios (Google) |
| `MODIFY_AUDIO_SETTINGS` | Normal | Modificar configurações de áudio |
| `BLUETOOTH`, `BLUETOOTH_ADMIN`, `BLUETOOTH_CONNECT` | Normal | Conexões Bluetooth |
| `START_ACTIVITIES_FROM_BACKGROUND` | Normal | Iniciar atividades em background |
| `com.huawei.permission.external_app_settings.USE_COMPONENT` | Normal | Suporte a dispositivos Huawei |
| `androidx.car.app.NAVIGATION_TEMPLATES`, `ACCESS_SURFACE` | Normal | Android Auto |

### ⚠️ FEATURES REQUERIDAS
| Feature | Requerido? | Função |
|-----------|-------------|--------|
| `android.hardware.location.gps` | ✅ Sim | Necessário para rastreamento |
| `android.hardware.sensor.light` | ❌ Não | Sensor de luz (opcional) |
| `android.hardware.camera` | ❌ Não | Câmera (opcional) |
| `android.hardware.microphone` | ❌ Não | Microfone (opcional) |
| `android.hardware.glEsVersion (0x00030000)` | ✅ Sim | OpenGL ES 2.0 |

## 3. BLOATWARE E SDKs DE MONETIZAÇÃO

### 📊 SISTEMA DE ANUNÇOS (AdMob / Google)
- **SDK**: `com.google.ads.mediation.admob.AdMobAdapter`
- **Mecanismos**:
  - `AdMobAdapter` (interstitial, native, custom tabs)
  - `AdMobOfflineBufferedPings.db` (arquivo SQLite de ping offline)
  - `gads:trustless_token_caching_in_signal_source_enabled`
  - `gads:firebase_analytics_integration:enabled`
- **Componentes de anúncio**:
  - `onAdClicked`, `onAdClosed`, `onAdFailedToLoad`, `onAdImpression`, `onAdLeftApplication`, `onAdLoaded`, `onAdOpened`, `onVideoEnd`
- **Palavras-chave**: `gads`, `admob`, `firebase_analytics`, `adInspector`, `warningType_`, `warningShownTimestampMsec_`, `warningShownInfo_`

### 📊 ANALYTICS E TRACKING
| SDK | Uso |
|-----|-----|
| Firebase Analytics | Eventos de uso, logEvent |
| Crashlytics | Relatórios de crash |
| Google Sign-In | Autenticação com Google |
| AdMob | Monetização |

### 📊 TELEMETRIA E RASTREAMENTO
O app coleta e envia os seguintes dados:
- Identidade do dispositivo (READ_PHONE_STATE)
- Número de telefone (READ_PHONE_STATE)
- Localização GPS (ACCESS_FINE_LOCATION)
- Localização em segundo plano (ACCESS_BACKGROUND_LOCATION)
- Contatos (READ_CONTACTS)
- Gravação de áudio (RECORD_AUDIO)
- Localização de WiFi e estado de rede (ACCESS_WIFI_STATE, CHANGE_WIFI_STATE, ACCESS_NETWORK_STATE)
- ID de anúncio (AD_ID)
- Histórico de consultas de rádio e alertas (warningType_, warningShownTimestampMsec_, warningShownInfo_)

## 4. ENGINE DE RADAR — Mapeamento Técnico

### 📍 GEOLOCALIZAÇÃO
| Componente | Função |
|----------|--------|
| `android.location.LocationManager` | Gerenciamento de localização via GPS/WiFi/Bluetooth |
| `FusedLocationProviderClient` | FusedLocationClient (Google Play Services) |
| `requestLocationUpdates()` | Atualizações contínuas de localização |
| `removeUpdates()` | Remoção de atualizações |
| `LocationListener` | Listener de atualizações de localização |

### 📍 ENGINE DE RADAR
| Componente | Função |
|----------|--------|
| `com.vialsoft.radarbot.navigation.ui.activities.NavSearchActivity` | Área de busca de rotas |
| `com.vialsoft.radarbot.navigation.ui.activities.NavSelectRouteActivity` | Seleção de rota |
| `com.vialsoft.radarbot.navigation.ui.activities.NavContactsActivity` | Gerenciamento de contatos |
| `com.vialsoft.radarbot.navigation.ui.activities.NavBookmarksActivity` | Bookmarks |
| `com.vialsoft.radarbot.navigation.ui.activities.NavFreeActivity` | Rota gratuita |
| `com.vialsoft.radarbot.useralerts.NotifyAlertActivity` | Notificação de alerta de radar |
| `com.vialsoft.radarbot.useralerts.EditRadarActivity` | Edição de alerta de radar |
| `com.vialsoft.radarbot.useralerts.PlaceUserMarkActivity` | Marcação de posição do usuário |
| `com.vialsoft.radarbot.auto.CarService` | Serviço do carro para Android Auto |
| `com.vialsoft.radarbot.firebaseNotification.MyFirebaseMessagingService` | Serviço Firebase Cloud Messaging |

### 🔑 Padrões de Localização (Smali)
| Método | Descrição |
|--------|---------|
| `requestLocationUpdates(String, long, float, LocationListener, Looper)` | Registro de atualizações de localização via GPS |
| `removeUpdates(LocationListener)` | Remoção de atualizações |

### 🔑 ENGINE DE ALERTA (Geofence/Proximity)
| Componente | Função |
|----------|--------|
| `com.vialsoft.radarbot.useralerts.NotifyAlertActivity` | Exibição de notificação de alerta de radar |
| `com.vialsoft.radarbot.useralerts.EditRadarActivity` | Edição de alerta de radar |
| `com.vialsoft.radarbot.useralerts.PlaceUserMarkActivity` | Marcação de posição do usuário |
| `com.vialsoft.radarbot.auto.CarService` | Serviço do carro (Android Auto) |
| `com.vialsoft.radarbot.firebaseNotification.MyFirebaseMessagingService` | FCM (mensageria push) |

### 🔑 DISTÂNCIA E VELOCIDADE
| Classe | Função |
|----------|--------|
| `android.location.Location` | Dados de localização (latitude, longitude, altitude, accuracy, velocity) |
| `android.location.LocationManager` | Gerenciador de fornecedores de localização |
| `com.vialsoft.radarbot.util.permissions.EJWJ` | Classe interna que gerencia tokens de permissão (contém strings como `XqwleKVaAnStM`, `tygx`, `RbcnLRhZXEDIT`, `KAIfb`) |

### 🔑 NOTIFICAÇÕES E ALERTAS
| Classe | Função |
|----------|--------|
| `com.vialsoft.radarbot.useralerts.NotifyAlertActivity` | Notificação de alerta de radar |
| `com.vialsoft.radarbot.useralerts.EditRadarActivity` | Edição de alerta de radar |
| `com.vialsoft.radarbot.useralerts.PlaceUserMarkActivity` | Marcação de posição do usuário |
| `a5.smali` | Métodos de `NotificationChannel` (c, d, e, f, j) |

## 5. ARQUITETURA DE LEVERAGEM (ANR, MEMÓRIA)

### ⚠️ GARGALOS IDENTIFICADOS

#### 1. Requisições de Localização em Segundo Plano
- **Risco**: `ACCESS_BACKGROUND_LOCATION` + `requestLocationUpdates()` constante
- **Impacto**: Uso excessivo de bateria e CPU
- **Solução**: Inativar localização de fundo, usar geofences otimizados, usar WorkManager em vez de foreground service

#### 2. Sistema de Anúncios (AdMob/Firebase)
- **Risco**: 45+ chamadas de rede por sessão, múltiplos SDKs de anúncio
- **Impacto**: Drenagem de bateria, latência de renderização
- **Solução**: Descartar AdSdk não essencial, limite de requisições, cache

#### 3. Múltiplas Activities com `launchMode="singleTask"`
- **Risco**: Leve sobrecarga de gerenciamento de activity stack
- **Impacto**: Pequeno atraso na navegação, possível ANR se stacks ficarem grandes
- **Solução**: Revisar configuração de launchMode para evitar acúmulo de activities

#### 4. Uso de `SYSTEM_ALERT_WINDOW`
- **Risco**: Segurança do sistema, consumo de memória, sobreposição de apps
- **Impacto**: Redução da experiência, possível constrição de desempenho
- **Solução**: Usar apenas quando necessário, com permissão explícita

#### 5. Requisições de Localização Constante (CarService)
- **Risco**: `requestLocationUpdates()` com intervalos curtos
- **Impacto**: Drenagem de bateria, consumo de CPU
- **Solução**: Aumentar intervalo, usar `fastestInterval`, implementar throttling

## 6. VERDICT DE SEGURANÇA

| Critério | Status | Comentário |
|----------|--------|---------|
| **Leveza** | ⚠️ Alto risco | Muitos services, múltiplas permissões, 149MB |
| **Segurança** | ⚠️ Alto risco | Permissões perigosas, tracker, API de localização de fundo |
| **Privacidade** | ⚠️ Alto risco | READ_PHONE_STATE, READ_CONTACTS, RECORD_AUDIO |
| **Monetização** | ⚠️ Alta (AdMob/Firebase) | O app é pago, mas contém anúncios dentro |
| **Arquitetura** | ⚠️ Complexa | 83 activities, 19 services, 23 receivers |

## 7. RECOMENDAÇÕES PARA UM SUPER APP SOBERANO

1. **Remover permissões perigosas**: `READ_PHONE_STATE`, `READ_CONTACTS`, `READ_EXTERNAL_STORAGE`
2. **Desativar localização em segundo plano**: Usar geofences otimizados, não background location
3. **Remover motores de anúncio**: Se o app é pago, não precisa de AdMob e Firebase Analytics
4. **Provarifcar arquivos de cache**: `AdMobOfflineBufferedPings.db`, `gads:trustless_token_caching`
5. **Limpeza de código**: Remover atividades não usadas (GoProGold, Tutorial, FragmentContainer, etc.)
6. **Otimizar localização**: Usar intervalo mais largo, throtling, foreground service apenas quando necessário
7. **Revisar.System.Alert.Window**: Endpoint em vez de overlay
8. **Limpar APK**: Remove SDKs não essenciais (AdMob, Firebase Analytics)

## 8. COMANDOS PARA LIMPEZA LOCAL

```bash
# Limpar diretório de extração (330MB)
rm -rf /home/swarm/ReaDroidLab/apk_decomp_radarbot

# Limpar APK original
rm -f /home/swarm/ReaDroidLab/APKs/Radarbot_Premium*.apk
```

---

*Blueprint gerado em: 2026-11-04*
*Veredito: **NÃO OTIMIZÁVEL PARA SUPER APP SOBERANO** — média de 45 permissões, 83 activities, 19 services, múltiplos SDKs de anúncio. Recomendável estudar a arquitetura do OpenTrack/Openradar e reescrever do zero.*
EOF
echo "Blueprint gerado com sucesso"
echo "Tamanho: $(wc -c < /home/swarm/ReaDroidLab/blueprint_radarbot_deepdive_20261104.md) bytes"