
<script setup>
import { ref } from 'vue'

const pagina = ref('login')

const usuario = ref('')
const senha = ref('')
const nome = ref('')
const email = ref('')

const usuarioLogado = ref('')
const usuarios = ref([])

const postagem = ref('')
const comentario = ref('')

const publicacoes = ref([
  {
    id: 1,
    usuario: 'Maria',
    texto: 'Bem-vindos ao Instabook! ❤️',
    curtidas: 12,
    comentarios: []
  },
  {
    id: 2,
    usuario: 'João',
    texto: 'Minha primeira publicação na rede social!',
    curtidas: 8,
    comentarios: []
  }
])

const amigos = ref(['Maria', 'João', 'Ana'])

function cadastrar() {
  if (!nome.value || !email.value || !senha.value) {
    alert('Preencha todos os campos!')
    return
  }

  if (usuarios.value.some(u => u.email === email.value)) {
    alert('Este e-mail já está cadastrado!')
    return
  }

  usuarios.value.push({
    nome: nome.value,
    email: email.value,
    senha: senha.value
  })

  usuarioLogado.value = nome.value
  pagina.value = 'feed'

  nome.value = ''
  email.value = ''
  senha.value = ''
}

function entrar() {
  const encontrado = usuarios.value.find(
    u => u.email === usuario.value && u.senha === senha.value
  )

  if (encontrado) {
    usuarioLogado.value = encontrado.nome
    pagina.value = 'feed'
  } else {
    alert('Usuário ou senha incorretos. Cadastre-se primeiro.')
  }
}

function curtir(post) {
  post.curtidas++
}

function comentar(post) {
  if (!comentario.value.trim()) return

  post.comentarios.push({
    usuario: usuarioLogado.value,
    texto: comentario.value
  })

  comentario.value = ''
}

function publicar() {
  if (!postagem.value.trim()) return

  publicacoes.value.unshift({
    id: Date.now(),
    usuario: usuarioLogado.value,
    texto: postagem.value,
    curtidas: 0,
    comentarios: []
  })

  postagem.value = ''
}

function adicionarAmigo(nomeAmigo) {
  if (!amigos.value.includes(nomeAmigo)) {
    amigos.value.push(nomeAmigo)
  }
}
</script>

<template>
  <main>
    <!-- LOGIN -->
    <section v-if="pagina === 'login'" class="login-container">
      <div class="login-card">
        <h1 class="logo">Instabook</h1>
        <p>Conecte-se com seus amigos!</p>

        <input
          v-model="usuario"
          type="email"
          placeholder="E-mail"
        />

        <input
          v-model="senha"
          type="password"
          placeholder="Senha"
        />

        <button @click="entrar">Entrar</button>

        <a @click="pagina = 'esqueci'">
          Esqueci minha senha
        </a>

        <hr />

        <button class="cadastro-btn" @click="pagina = 'cadastro'">
          Criar nova conta
        </button>
      </div>
    </section>

    <!-- CADASTRO -->
    <section v-else-if="pagina === 'cadastro'" class="login-container">
      <div class="login-card">
        <h1 class="logo">Instabook</h1>
        <h2>Criar conta</h2>

        <input v-model="nome" placeholder="Nome completo" />
        <input v-model="email" type="email" placeholder="E-mail" />
        <input v-model="senha" type="password" placeholder="Criar senha" />

        <button @click="cadastrar">Cadastrar</button>

        <a @click="pagina = 'login'">
          Já tenho uma conta
        </a>
      </div>
    </section>

    <!-- RECUPERAÇÃO DE SENHA -->
    <section v-else-if="pagina === 'esqueci'" class="login-container">
      <div class="login-card">
        <h1 class="logo">Instabook</h1>
        <h2>Recuperar senha</h2>

        <p>Informe seu e-mail para solicitar a recuperação.</p>

        <input type="email" placeholder="Seu e-mail" />

        <button @click="alert('Configure o envio de recuperação pelo backend.')">
          Recuperar senha
        </button>

        <a @click="pagina = 'login'">Voltar ao login</a>
      </div>
    </section>

    <!-- FEED DA REDE SOCIAL -->
    <section v-else-if="pagina === 'feed'" class="feed">

      <header class="topbar">
        <h1 class="logo">Instabook</h1>
        <span>Olá, {{ usuarioLogado }}!</span>
        <button @click="pagina = 'login'; usuarioLogado = ''">
          Sair
        </button>
      </header>

      <div class="feed-layout">

        <aside class="sidebar">
          <h3>Menu</h3>
          <p @click="pagina = 'feed'">🏠 Página inicial</p>
          <p @click="pagina = 'amigos'">👥 Amigos</p>
          <p @click="pagina = 'perfil'">👤 Meu perfil</p>
        </aside>

        <div class="conteudo">

          <div class="criar-post">
            <h2>O que está acontecendo?</h2>

            <textarea
              v-model="postagem"
              placeholder="Compartilhe uma publicação..."
            ></textarea>

            <button @click="publicar">
              Compartilhar postagem
            </button>
          </div>

          <article
            v-for="post in publicacoes"
            :key="post.id"
            class="post"
          >
            <h3>👤 {{ post.usuario }}</h3>

            <p>{{ post.texto }}</p>

            <button @click="curtir(post)">
              ❤️ Curtir ({{ post.curtidas }})
            </button>

            <div class="comentarios">
              <h4>Comentários</h4>

              <p
                v-for="(item, index) in post.comentarios"
                :key="index"
              >
                <strong>{{ item.usuario }}:</strong>
                {{ item.texto }}
              </p>

              <input
                v-model="comentario"
                placeholder="Escreva um comentário..."
                @keyup.enter="comentar(post)"
              />

              <button @click="comentar(post)">
                Comentar
              </button>
            </div>
          </article>

        </div>
      </div>
    </section>

    <!-- AMIGOS -->
    <section v-else-if="pagina === 'amigos'" class="feed">
      <header class="topbar">
        <h1 class="logo">Instabook</h1>
        <button @click="pagina = 'feed'">Voltar</button>
      </header>

      <div class="conteudo">
        <h2>Adicionar amigos 👥</h2>

        <div v-for="amigo in ['Carlos', 'Fernanda', 'Pedro', 'Julia']"
             :key="amigo" class="post">
          <strong>{{ amigo }}</strong>
          <button @click="adicionarAmigo(amigo)">
            {{ amigos.includes(amigo) ? 'Adicionado ✓' : 'Adicionar amigo' }}
          </button>
        </div>

        <h3>Meus amigos</h3>
        <p v-for="amigo in amigos" :key="amigo">
          👤 {{ amigo }}
        </p>
      </div>
    </section>

    <!-- PERFIL -->
    <section v-else-if="pagina === 'perfil'" class="feed">
      <header class="topbar">
        <h1 class="logo">Instabook</h1>
        <button @click="pagina = 'feed'">Voltar</button>
      </header>

      <div class="conteudo post">
        <h2>Meu perfil 👤</h2>
        <h3>{{ usuarioLogado }}</h3>
        <p>Bem-vindo ao seu perfil Instabook!</p>
        <p>Publicações: {{ publicacoes.filter(p => p.usuario === usuarioLogado).length }}</p>
      </div>
    </section>
  </main>
</template>




<style scoped>
  main {

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: #fafafa;
  color: #262626;
}

.login-container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  background: linear-gradient(
    135deg,
    #833ab4,
    #fd1d1d,
    #fcb045
  );
}

.login-card {
  background: white;
  padding: 35px;
  border-radius: 12px;
  width: 100%;
  max-width: 380px;
  text-align: center;
  box-shadow: 0 5px 25px #0003;
}

.logo {
  font-family: cursive;
  font-size: 38px;
  color: #c13584;
  margin-bottom: 15px;
}

.login-card p {
  margin-bottom: 20px;
}

input, textarea {
  width: 100%;
  padding: 13px;
  margin: 8px 0;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 14px;
}

textarea {
  min-height: 90px;
  resize: vertical;
}

button {
  background: linear-gradient(45deg, #833ab4, #fd1d1d);
  color: white;
  padding: 12px 18px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  margin: 8px 0;
  font-weight: bold;
}

button:hover {
  opacity: .85;
}

.login-card button {
  width: 100%;
}

.login-card a {
  display: block;
  color: #833ab4;
  margin: 15px;
  cursor: pointer;
}

.cadastro-btn {
  background: #269c46;
}

.feed {
  min-height: 100vh;
}

.topbar {
  background: white;
  padding: 15px 5%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 1px 5px #ddd;
}

.topbar .logo {
  margin: 0;
}

.feed-layout {
  display: grid;
  grid-template-columns: 230px 1fr;
  gap: 30px;
  max-width: 1100px;
  margin: 30px auto;
  padding: 15px;
}

.sidebar {
  background: white;
  padding: 20px;
  border-radius: 10px;
  height: fit-content;
}

.sidebar p {
  padding: 15px 0;
  cursor: pointer;
}

.conteudo {
  width: 100%;
  max-width: 650px;
  margin: 30px auto;
  padding: 15px;
}

.criar-post, .post {
  background: white;
  padding: 22px;
  border: 1px solid #ddd;
  border-radius: 10px;
  margin-bottom: 20px;
}

.post h3 {
  margin-bottom: 15px;
}

.post > p {
  margin: 15px 0;
}

.comentarios {
  border-top: 1px solid #eee;
  margin-top: 15px;
  padding-top: 15px;
}

@media (max-width: 700px) {
  .feed-layout {
    grid-template-columns: 1fr;
  }

  .sidebar {
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
  }

  .sidebar h3 {
    width: 100%;
  }

  .topbar {
    flex-wrap: wrap;
    gap: 10px;
  }
}

    }
</style>