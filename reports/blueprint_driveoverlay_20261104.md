# 📐 Blueprint Arquitetural — Drive Overlay

## 1. METADADOS DO APLICATIVO
| Campo | Valor |
|---------|-------|
| **Package** | `com.fantom9s.speedbatteryoverlay` |
| **Versão** | `1.1` (code 2) |
| **Min SDK** | 32 (Android 12) |
| **Target SDK** | 36 (Android 16) |
| **Base APK** | 36MB |
| **Tamanho Total** | 37MB (XAPK) |
| **Split APKs** | config.arm64_v8a (64KB), config.en (108KB), config.pt (64KB), config.xhdpi (92KB) |
| **Tipo** | Apps de overlay de bateria + anúncios (Yandex Ad) |
| **Estado** | `ANÁLISE EM ANDAMENTO` |

## 2. ESTRUTURA DE COMPONENTES
| Tipo | Componente | Função |
|----------|-----------|--------|
| Activity | `MainActivity` | Tela principal (launcher) |
| Service | `OverlayService` | Overlay de bateria (SYSTEM_ALERT_WINDOW) |
| Service | `AppMetricaService` | Analytics (Yandex AppMetrica) |
| Activity | `AdActivity` | Renderização de anúncios (Yandex Ads) |
| Activity | `IntegrationInspectorActivity` | Painel de diagnóstico (debug) |
| Activity | `LicenseActivity` | Verificação de licença (PairIP) |
| Provider | `YandexAdsInitializeProvider` | Inicialização SDK Yandex Ads |
| Provider | `DebugPanelFileProvider` | Arquivos de diagnóstico |
| Provider | `PreloadInfoContentProvider` | Pré-carregamento de informações |
| Provider | `FileProvider` | Compartilhamento de arquivos |

## 3. ARQUITETURA DE PERMISSÕES
| Permissão | Nível | Função |
|-----------|-------|--------|
| `SYSTEM_ALERT_WINDOW` | Normal | Overlay sobre apps (janelas sobre a aplicação) |
| `ACCESS_FINE_LOCATION` | Dangerous | Localização precisa (para rastreamento de bateria) |
| `ACCESS_COARSE_LOCATION` | Dangerous | Localização aproximada |
| `FOREGROUND_SERVICE` | Normal | Serviço em segundo plano |
| `FOREGROUND_SERVICE_LOCATION` | Normal | Serviço de localização em foreground |
| `POST_NOTIFICATIONS` | Normal | Notificações |
| `INTERNET` | Normal | Conexão com API |
| `ACCESS_NETWORK_STATE` | Normal | Estado de rede |
| `com.google.android.gms.permission.AD_ID` | Normal | Identificador de anúncios |

### 🛡️ Segurança Específica
- **Overlay**: `SYSTEM_ALERT_WINDOW` permite que o app exiba sobre outros apps
- **Localização**: Requer permissão de localização para monitorar bateria
- **Anúncios**: Integração com Yandex Ads SDK + Google Play Services (AD_ID)

## 4. FLUXO DE DADOS (Mapeamento de Engenharia)
### 🔹 Overlay de Bateria (Core)
1. `OverlayService` inicia em foreground com `SYSTEM_ALERT_WINDOW`
2. Mostra overlay sobre aplicativos de terceiros
3. Monitora bateria e performance do dispositivo
4. Envia dados para `AppMetrica Service` (analytics)

### 🔹 Anúncios (Yandex Ads)
1. `YandexAdsInitializeProvider` inicializa SDK automaticamente
2. `AdActivity` renderiza anúncios de banner/interstitial
3. `IntegrationInspectorActivity` permite debugging de integração
4. `FileProvider` expõe arquivos de anúncios privados

### 🔹 Licença (PairIP)
1. `LicenseActivity` verifica validade da licença
2. Verifica se o app está pago ou gratuito
3. Controla funcionalidades avançadas

## 5. BIBLIOTECAS DE TERCEIROS EVTAS 
| Biblioteca | Uso |
|------------|-----|
| Yandex Mobile Ads SDK | Anúncios de display |
| AppMetrica Analytics | Analytics e monitoramento |
| Google Play Services | Locação de anúncios e serviços |
| PairIP License Check | Verificação de licença |

### 🌐 Chamadas de API Externas
- **Yandex Ads API**: Exibição de anúncios
- **AppMetrica API**: Envio de dados de uso
- **Google Play Services API**: Locação de anúncios

## 6. REQUISITOS
- **Camera**: Não obrigatória
- **Microfone**: Obrigatório (parte do overlay de bateria)
- **Armazenamento**: Leitura/Escrita (para cache de anúncios)
- **Network**: Reactor

## 7. NOTAS DE SEGURANÇA
⚠️ O app solicita `SYSTEM_ALERT_WINDOW` e `ACCESS_FINE_LOCATION` — 
funcionalidades que devem ser revistas para menos permissões quando não for essencial.

> **Source:** DriveOverlay (com.fantom9s.speedbatteryoverlay) v1.1
