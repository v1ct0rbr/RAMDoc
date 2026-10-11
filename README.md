<div align="center">

# 🚀 RAMDoc
## Monitor de Desempenho & Otimizador de Memória para Linux

<img src="https://img.shields.io/badge/Made%20with-Python-3776ab.svg?style=for-the-badge&logo=python&logoColor=white" alt="Python">
<img src="https://img.shields.io/badge/Platform-Linux-green.svg?style=for-the-badge&logo=linux&logoColor=white" alt="Linux">
<img src="https://img.shields.io/badge/Status-Active-brightgreen.svg?style=for-the-badge" alt="Active">
<img src="https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge" alt="MIT License">

<img src="ramdoc.svg" width="96" alt="RAMDoc icon">

*Ferramenta de monitoramento em tempo real, auto-liberação de memória e limpeza segura do sistema — via terminal, dashboard ao vivo ou bandeja do sistema.*

**Por [Victor Queiroga](https://github.com/v1ct0rbr)**

</div>

---

## 🌟 Recursos

<table cellpadding="0" cellspacing="0" style="border-collapse: collapse; border: none;">
<tr>
<td align="left" valign="top" style="padding: 10px; border: none; width: 50%;">

### 🧠 Memória & CPU
- 📊 RAM, SWAP e pressão de memória (PSI) em tempo real
- ⚡ Uso de CPU, núcleos, frequência e load average
- 👑 Top consumidores de memória (somente leitura)
- 🧹 Limpeza de page cache (`drop_caches`) com um comando

### 💾 Armazenamento
- 📈 Uso de disco e espaço livre
- 🔍 Detecção de arquivos grandes (varredura somente leitura)
- 🗑️ Limpeza segura de arquivos temporários (pula arquivos em uso e recentes)

</td>
<td align="left" valign="top" style="padding: 10px; border: none;">

### 📡 Monitor ao vivo (`--watch`)
- 🖥️ Dashboard em tela cheia (RAM, SWAP, CPU, DISK, PSI)
- 📈 Sparkline de histórico de RAM
- 🤖 **Auto-free**: limpa o page cache quando a RAM cruza o seu limite
- ⏱️ Intervalo, limite e cooldown configuráveis

### 📌 Bandeja do sistema (`--tray`)
- 🎛️ Ícone residente com medidor de RAM (verde/amarelo/vermelho)
- 🧹 Limpeza de cache e temp com um clique pelo menu
- ✓ **Auto-free** ligável pelo mesmo menu (mesmas regras do `--watch`)
- 🔔 Notificações desktop para cada ação

</td>
</tr>
</table>

---

## 🎮 Uso

### 🎪 Modo interativo
```bash
./RAMDoc          # ou ./ramdoc
```

### 💻 Linha de comando
```bash
./RAMDoc --status               # Status rápido do sistema
./RAMDoc --clean                # Limpar page cache (sudo)
./RAMDoc --large-files          # Caçar arquivos grandes
./RAMDoc --temp-clean           # Limpar arquivos temporários
./RAMDoc --processes            # Top processos por memória
./RAMDoc --watch                # Dashboard ao vivo + auto-free a 85% de RAM
./RAMDoc --watch 5 --threshold 90 --cooldown 120  # Monitor customizado
./RAMDoc --watch --no-auto-free # Só monitorar, nunca limpar cache
./RAMDoc --tray                 # Ícone na bandeja (precisa de pystray)
./RAMDoc --tray --threshold 90  # Bandeja + auto-free a 90% de RAM

# Opções avançadas
./RAMDoc --large-files --directory /path --size 500 --limit 10
./RAMDoc --temp-clean --dry-run          # Só mostrar o que seria apagado
./RAMDoc --temp-clean --yes --min-age 3  # Não-interativo, arquivos ≥ 3 dias
```

---

## 📦 Requisitos & Instalação

- 🐍 Python 3.6+
- 📊 psutil (`pip3 install psutil` ou `sudo apt install python3-psutil`)
- 🐧 Linux
- 👑 sudo (para a limpeza de cache)
- 📌 *Opcional — só para o ícone de bandeja:* `python3-pystray`, `python3-pil` e `gir1.2-ayatanaappindicator3-0.1`

### Instalação
```bash
git clone https://github.com/v1ct0rbr/RAMDoc.git
cd RAMDoc
chmod +x RAMDoc ramdoc
pip3 install psutil
./RAMDoc
```

### 🤖 Auto-free (watch e bandeja)
O auto-free limpa o page cache quando a RAM cruza o seu limite. Precisa
de sudo não-interativo:
```bash
sudo -v              # Guarda o ticket do sudo (dura ~15 min)
./RAMDoc --watch     # Monitor + auto-free habilitado
# ou rode como root:
sudo ./RAMDoc --watch
```

### 📌 Ícone de bandeja
```bash
# Debian/Ubuntu
sudo apt install python3-pystray python3-pil gir1.2-ayatanaappindicator3-0.1
# ou via pip
pip3 install pystray Pillow

./RAMDoc --tray
```

**Notas de desktop:**
- **KDE / XFCE / Cinnamon / MATE:** funciona direto
- **GNOME:** instale antes a extensão
  [AppIndicator and KStatusNotifierItem Support](https://extensions.gnome.org/extension/615/appindicator-support/)
  — o GNOME esconde ícones de bandeja por padrão
- A cor do ícone acompanha a pressão de RAM: 🟢 &lt; 50% · 🟡 &lt; 80% · 🔴 ≥ 80%
- **Nunca rode `--tray` com `sudo`** — a bandeja precisa entrar no session bus
  do seu usuário e o sudo o remove (o ícone não aparece ou o startup falha
  com erro de `dbus-launch`). Rode como seu usuário; para o auto-free, rode
  `sudo -v` num terminal antes
- O menu da bandeja tem RAM/SWAP ao vivo, ações manuais e um item
  **Auto-free** marcável (mesmas regras do `--watch`: padrão ≥ 85% com
  cooldown de 60s — ajuste com `--threshold` / `--cooldown`, desligue com
  `--no-auto-free`). Sem sudo válido, o auto-free começa desligado e se
  desliga sozinho com notificação se o sudo falhar

Defina `NO_COLOR=1` para desabilitar cores ANSI (útil em logs/pipes).

---

## 🛡️ Segurança

| 🔒 Recurso | 📝 Descrição |
|-------------------|----------------|
| 🕵️ **Varredura não-destrutiva** | Detecção de arquivos grandes é somente leitura |
| 🧹 **Limpeza segura** | Só toca locais padrão de temp; pula arquivos em uso e recentes (idade mínima aplicada) |
| ✋ **Confirmação** | A limpeza de temp pergunta antes de apagar (ou exige `--yes` em modo não-interativo) |
| 👀 **Monitoramento de processos** | Sem capacidade de matar processos |
| 🤖 **Auto-free limitado** | Tem cooldown e reporta exatamente o que liberou |
| 🚨 **Tratamento de erros** | Códigos de saída corretos (0 sucesso, 1 erro, 2 uso inválido, 130 interrupção) |

---

## 🎪 Menu interativo

| Opção | Ação | Descrição |
|-----------|-----------|----------------|
| **1** | 📊 **Status do sistema** | RAM, SWAP, CPU, PSI e armazenamento |
| **2** | 🧹 **Limpar cache de memória** | Libera memória (precisa de sudo) |
| **3** | 🔍 **Arquivos grandes** | Busca arquivos acima do limite de tamanho |
| **4** | 🗑️ **Limpar temporários** | Remove arquivos temp/cache (pede confirmação) |
| **5** | 👑 **Top memória** | Lista os processos que mais comem RAM |
| **6** | 📡 **Monitor ao vivo** | Dashboard em tempo real + auto-free (Ctrl+C para parar) |
| **7** | 🚀 **Limpeza completa** | Otimização completa do sistema |
| **8** | 📌 **Ícone de bandeja** | Bandeja residente com medidor de RAM e ações rápidas |
| **0** | 👋 **Sair** | Tchau! |

---

## 🎨 Exemplos

### 🖥️ Status do sistema
```
════════════════════ SYSTEM STATUS ═══════════════════
2026-10-10 21:12:33  ·  uptime 03:23:22

🧠 MEMORY
   RAM   ████████████████▋░░░░░░░░░░░  59.0%  8.6 GB / 14.6 GB  (avail 6.0 GB)
   SWAP  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░   0.0%  972.0 KB / 4.0 GB
   PSI   memory pressure avg10 = 0.00  (0=idle, 100=stalled)

⚡ CPU
   █▌░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░   5.0%  16 cores  load 3.17 3.09 2.46

💾 STORAGE
   ████████▊░░░░░░░░░░░░░░░░░░░░░░░░  31.2%  138.1 GB / 466.3 GB  (free 304.4 GB)
```

### 📡 Monitor ao vivo (`--watch`)
```
╭─ RAMDoc · LIVE MONITOR · 21:12:35 ────────────────────────────────────────────╮
│ RAM   ████████████████▋░░░░░░░░░░░  59.0%  8.6 GB/14.6 GB avail 6.0 GB       │
│ SWAP  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░   0.0%  972.0 KB/4.0 GB                   │
│ CPU   █████████████▍░░░░░░░░░░░░░░░  33.2%  16 cores  load 2.83 3.02         │
│ DISK  ████████▊░░░░░░░░░░░░░░░░░░░░  31.2%  free 304.4 GB                    │
│ PSI   memory pressure avg10 = 0.00                                            │
│                                                                                │
│ TOP MEMORY                                                                     │
│     18463    7.7%    0.0%  QtWebEngineProcess                                  │
│      3287    6.5%    0.0%  clamd                                               │
│                                                                                │
│ RAM HISTORY last 120 samples                                                   │
│   ▁▂▃▅▆▅▄▃▂▁▁▂▄▆█▇▅▄▃▂▁▂▄▆█▇▆▄▂▁▂▄▆█▇▆▄▂▁▂▄▆█▇▆▄▂▁▂▄▆█▇▆▄▂▁▂▄▆█▇▆▄▂▁▂▄▆█▆▄▂ │
╰────────────────────────────────────────────────────────────────────────────────╯
 Ctrl+C to stop · auto-free at ≥85% RAM, cooldown 60s
```

---

## 🤝 Contribuindo

1. 🍴 Fork do repositório
2. 🌟 Crie sua branch (`git checkout -b feature/AlgoIncrivel`)
3. 💫 Commit (`git commit -m 'Adiciona AlgoIncrivel'`)
4. 🚀 Push (`git push origin feature/AlgoIncrivel`)
5. 🎁 Abra um Pull Request

---

## 📜 Licença & Créditos

Licenciado sob a MIT License — veja o arquivo [LICENSE](LICENSE) para detalhes.

O RAMDoc começou como um fork do
[MEM-CLEAN](https://github.com/itsWickedSyntax/MEM-CLEAN) de
[WickedSyntax](https://github.com/WickedSyntax), e foi substantialmente
reescrevido (monitor ao vivo, bandeja com auto-free, limpeza segura de temp,
tratamento de erros e muito mais). Agradecimentos ao original!

**Livre para usar, modificar e distribuir! 🎉**

<div align="center">

### 💖 Feito com Python por [Victor Queiroga](https://github.com/v1ct0rbr)

**"Monitorando memória, liberando RAM!"**

</div>
