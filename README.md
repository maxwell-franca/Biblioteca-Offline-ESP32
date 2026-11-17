# 📚 Biblioteca Offline ESP32

**Biblioteca comunitária sem internet: o ESP32 cria o próprio Wi-Fi, abre um captive portal protegido por senha e serve arquivos de um cartão SD, com um mural de recados.**

![ESP32](https://img.shields.io/badge/ESP32-E7352C?style=for-the-badge&logo=espressif&logoColor=white)
![Arduino](https://img.shields.io/badge/Arduino-00878F?style=for-the-badge&logo=arduino&logoColor=white)
![C++](https://img.shields.io/badge/C++-00599C?style=for-the-badge&logo=cplusplus&logoColor=white)
![Licença](https://img.shields.io/badge/Licen%C3%A7a-MIT-green?style=for-the-badge)

---

## 🧠 O que é

Um projeto embarcado que transforma um ESP32 em um ponto de acesso com conteúdo local, útil onde não há internet (comunidades, escolas, postos de saúde, oficinas):

- 📡 **Wi-Fi próprio (WPA2):** o ESP32 cria a rede `Biblioteca-Bairro` e atende até 4 aparelhos ao mesmo tempo.
- 🪤 **Captive portal:** um DNS que responde a qualquer domínio com o IP do ESP32 e as páginas de teste do Android, iOS e Windows fazem o celular abrir a biblioteca sozinho ao conectar.
- 🔐 **Acesso por senha:** login com sessão por cookie e bloqueio temporário após erros de senha.
- 📂 **Biblioteca de arquivos:** navegação por pastas e download de PDFs, textos, imagens e mapas guardados no cartão SD.
- 📝 **Mural:** quadro de recados da comunidade, salvo no próprio armazenamento.

---

## ✨ Funcionalidades

- Rede Wi-Fi WPA2 criada pelo ESP32, em `192.168.4.1`
- Captive portal com DNS curinga e URLs de detecção dos principais sistemas
- Login por senha, cookie `HttpOnly` de 12 horas e até 8 sessões simultâneas
- Bloqueio por 60 s após 5 senhas erradas (valores configuráveis)
- Navegador de pastas (até 200 itens por pasta) com visualização e download
- Proteção contra `../` e caminhos inválidos, e escape de HTML em tudo que é exibido
- Mural com nome (até 20 caracteres) e mensagem (até 200), mostrando as 30 mais recentes
- Cartão SD como armazenamento principal, com memória interna (LittleFS) como alternativa para testes
- Pastas criadas automaticamente: `/saude`, `/plantas`, `/manuais` e `/mapas`
- Usa só bibliotecas que já vêm com o core do ESP32, sem nada para instalar

---

## 🔧 Hardware

| Componente | Observação |
| --- | --- |
| ESP32 | Placa de desenvolvimento (WROOM, "ESP32 Dev Module") |
| Módulo leitor de cartão microSD | Interface SPI (VSPI), opcional para testes |
| Cartão microSD | Formatado em FAT32, com os arquivos da biblioteca |
| Cabo USB | Para gravar e alimentar |

### Ligação do módulo SD (VSPI)

| Módulo SD | ESP32 |
| --- | --- |
| CS | GPIO 5 |
| SCK | GPIO 18 |
| MISO | GPIO 19 |
| MOSI | GPIO 23 |
| VCC | 5V (VIN), na maioria dos módulos com regulador |
| GND | GND |

---

## 📁 Estrutura

```
biblioteca-esp32/
├── biblioteca_esp32/
│   └── biblioteca_esp32.ino   # código principal
├── COMMITS.md                 # padrão de commits do projeto
├── LICENSE
└── README.md
```

A pasta do sketch precisa ter o mesmo nome do arquivo `.ino` para a Arduino IDE abri-lo.

---

## 🚀 Como usar

1. **Clone o repositório**
   ```
   git clone https://github.com/maxwell-franca/biblioteca-esp32.git
   ```
2. **Troque as duas senhas** no bloco `CONFIGURE AQUI` do `biblioteca_esp32.ino` antes de gravar:
   ```cpp
   const char* AP_WIFI_PASS = "TROQUE-wifi-123";    // senha do Wi-Fi (mínimo 8 caracteres)
   const char* PANEL_PASS   = "TROQUE-painel-456";  // senha do painel (login)
   ```
   Se a senha do Wi-Fi tiver menos de 8 caracteres, a rede não é criada. **Não envie as suas senhas reais ao GitHub:** mantenha os valores `TROQUE-...` no repositório.
3. **Abra o `biblioteca_esp32.ino`** na Arduino IDE, instale o pacote **esp32 by Espressif** no Gerenciador de Placas e selecione a placa **ESP32 Dev Module**, com a partição **Default 4MB with spiffs**.
4. **Grave no ESP32** e abra o Monitor Serial (115200 baud). Ele informa se está usando o cartão SD ou a memória interna.
5. **Coloque os arquivos no cartão SD**, nas pastas `/saude`, `/plantas`, `/manuais` e `/mapas`, usando nomes curtos, sem acentos e sem espaços.
6. **Conecte-se à rede** `Biblioteca-Bairro` pelo celular. O portal abre sozinho; se não abrir, acesse **192.168.4.1** no navegador.
7. **Entre com a senha do painel** e navegue pelos arquivos ou pelo mural.

> Sem cartão SD, o sketch usa a memória interna (LittleFS), que serve só para testar: o sketch não tem envio de arquivos pelo navegador, então ela contém apenas o `LEIA-ME.txt` criado automaticamente.

> Se o download não funcionar na janela do portal, abra o navegador normal do celular e acesse `192.168.4.1`.

---

## ⚠️ Segurança e limitações

- **Sem HTTPS:** a senha do painel trafega em HTTP. O Wi-Fi é WPA2, mas quem conhece a senha da rede consegue, em tese, observar o tráfego dos outros. Use senhas diferentes para o Wi-Fi e o painel e **nunca reutilize uma senha pessoal**.
- **Bloqueio global:** o contador de erros é único. Uma pessoa errando a senha bloqueia o login de todos por 60 s.
- **Sessões não expiram no servidor:** o cookie dura 12 horas, mas as 8 vagas só são liberadas por logout ou quando uma nova sessão ocupa o lugar da mais antiga.
- **Servidor de uma conexão por vez:** o download de um arquivo grande deixa as demais requisições esperando.
- **Mural sem moderação:** qualquer pessoa autenticada publica. O arquivo é apagado quando passa de 100 KB, e o horário exibido é "minutos após ligar", que reinicia a cada reinício do ESP32.
- **Sem internet:** o DNS curinga aponta todos os domínios para o ESP32, então os aparelhos conectados não navegam fora da biblioteca.

---

## 📝 Padrão de commits

Este projeto usa commits com gitmoji. Os detalhes estão em [COMMITS.md](COMMITS.md).

---

## 📄 Licença

Distribuído sob a licença **MIT**. Veja o arquivo [LICENSE](LICENSE).

---

## 👤 Autor

**Maxwell França** · Contador & Administrador | IA, Automação e Sistemas Embarcados  
🇧🇷 São Luís – MA, Brasil

<a href="https://github.com/maxwell-franca">
  <img src="https://img.shields.io/badge/GitHub-maxwell--franca-181717?style=for-the-badge&logo=github" />
</a>

---

⭐ Se gostou do projeto, deixe uma estrela no repositório
