# 📐 Blueprint Arquitetural — Rota Pro

## 1. METADADOS DO APLICATIVO
| Campo | Valor |
|---------|-------|
| **Package** | `app.rotapro` |
| **Versão** | `1.1.70` (code 83) |
| **Min SDK** | 32 (Android 12) |
| **Target SDK** | 36 (Android 16) |
| **Compile SDK** | 36 |
| **Tipo** | Flutter-based app (FlutterEmbedding meta-data present) |
| **Estado** | `SOVEREANO` (em análise) |

## 2. ESTRUTURA DE COMPONENTES
| Tipo | Componente | Função |
|----------|-----------|--------|
| Activity | `MainActivity` | Tela inicial |
| Activity | `NavChooserActivity` | Seleção de roteiro |
| Activity | `WebViewActivity` | Renderização web interna |
| Service | `RotaAccessibilityService` | Interface de acessibilidade (overlay) |
| Service | `CameraRecordingService` | Gravação de vídeo (câmera) |
| Service | `ScreenCaptureService` | Captura de tela |
| Service | `BubbleService` | Bolinha flutuante (overlay) |
| Service | `RotaMessagingService` | Mensageria (Firebase) |
| Receiver | `FinanceBootReceiver` | Inicialização de financas |
| Receiver | `FinanceReminderReceiver` | Lembrete de financas |

## 3. ARQUITETURA DE PERMISSÕES
| Permissão | Nível | Função |
|-----------|-------|--------|
| `CAMERA` | Normal | Captura de vídeo/áudio |
| `RECORD_AUDIO` | Normal | Gravação de áudio |
| `FOREGROUND_SERVICE_CAMERA` | Normal | Serviço de câmera |
| `FOREGROUND_SERVICE_MICROPHONE` | Normal | Serviço de áudio |
| `SYSTEM_ALERT_WINDOW` | Normal | Overlay sobre apps |
| `INTERNET` | Normal | Conexão com API |
| `ACCESS_ADSERVICES_*` | Normal | Análise de publicidade |
| `POST_NOTIFICATIONS` | Normal | Notificações |
| `RECEIVE_BOOT_COMPLETED` | Normal | Inicialização automática |
| `VIBRATE` | Normal | Vibração |
| `ACCESS_FINE_LOCATION` | Dangerous | Localização (provedor) |
| `READ_CONTACTS` | Dangerous | Acesso a contatos |
| `ACCESS_NETWORK_STATE` | Normal | Estado de rede |
| `WAKE_LOCK` | Normal | Manter CPU ativo |

### 🛡️ Segurança Específica
- **Acessibilidade**: `RotaAccessibilityService` com `android.accessibilityservice.AccessibilityService` — permite interceptar eventos de tela e cliques
- **Overlay**: `SYSTEM_ALERT_WINDOW` + `BubbleService` (foreground service type `0x40000000`) — exibição de informações sobre a tela
- **Gravação**: `CameraRecordingService` (foreground service type `0xc0`) e `ScreenCaptureService` (foreground service type `0x20`)

## 4. FLUXO DE DADOS (Mapeamento de Engenharia)
### 🔹 OCR / Reconhecimento de Texto
1. Câmera captura quadro → `CameraRecordingService`
2. Frame enviado para MLKit (Google ML Kit)
3. MLKit extrai texto via OCR
4. Resultado parseado por `receita_android.json` (regex configurados)
5. Dados estruturados em `flutter_assets`

### 🔹 Interceptação de Áudio
1. Microfone captura áudio → `RecordingService`
2. Áudio processado em tempo real
3. Transcrito via API de voz em nuvem

### 🔹 Sobreposição de Tela
1. `ScreenCaptureService` captura a tela
2. `BubbleService` exibe informações sobrepostas
3. `RotaAccessibilityService` intercepta eventos de acessibilidade

### 🔹 Background Services
- `FlutterFirebaseMessagingBackgroundService` (Firebase)
- `RotaMessagingService` (Firebase)
- `FinanceBootReceiver` (inicialização)
- `FinanceReminderReceiver` (lembretes)

## 5. BIBLIOTECAS DE TERCEIROS EVTAS 
| Biblioteca | Uso |
|------------|-----|
| Google MLKit | OCR, reconhecimento de texto/vídeo |
| Firebase | Mensageria, armazenamento, analytics |
| Flutter | Framework de UI (FlutterEmbedding) |
| AndroidX | Bibliotecas Android modernas |
| AdMob | Anúncios |

### 🌐 Chamadas de API Externas (Estimadas)
- OAuth 2.0 + Firebase Auth
- Google Cloud Vision API (OCR)
- Firebase Cloud Messaging (FCM)
- AdMob (anúncios)
- API de navegação/roteamento externa (intents de `com.waze`, `google.android.apps.maps`)

## 6. REQUISITOS
- **Caméra**: Obrigatória (`android.hardware.camera.any` required)
- **Microfone**: Obrigatório
- **Network**: Reativa
- **Armazenamento**: Leitura/Escrita (máx. SDK 28)

## 7. NOTAS DE SEGURANÇA
⚠️ O app solicita `SYSTEM_ALERT_WINDOW` (overlay) e `ACCESS_FINE_LOCATION` + `READ_CONTACTS` — funcionalidades que devem ser revistas para menos permissões quando não for essencial.
