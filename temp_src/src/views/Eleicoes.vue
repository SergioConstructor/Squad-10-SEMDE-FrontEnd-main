<template>
  <div class="dashboard-eleicoes">
    <header class="dashboard-header">
      <h1>Dashboard de Eleições</h1>
      <div class="filtros">
        <select v-model="filtroMunicipio" @change="fetchData">
          <option :value="null" disabled>Selecione um Município</option>
          <option v-for="municipio in municipios" :key="municipio.id" :value="municipio.id">
            {{ municipio.nome }}
          </option>
        </select>
        <select v-model="filtroAno" @change="fetchData">
           <option :value="null" disabled>Selecione o Ano</option>
          <option>2024</option>
          <option>2022</option>
        </select>
      </div>
    </header>

    <div v-if="loading" class="loading">Carregando dados...</div>

    <div v-if="!loading && dadosSumario" class="dashboard-content">
      <section class="summary-cards">
        <div class="card">
          <h3>Total de Votos Apurados</h3>
          <p>{{ dadosSumario.totalVotosApurados }}</p>
        </div>
        <div class="card">
          <h3>Bairros Mapeados</h3>
          <p>{{ dadosSumario.totalBairrosMapeados }}</p>
        </div>
        <div class="card">
          <h3>Candidato Mais Votado</h3>
          <p>{{ dadosSumario.candidatoMaisVotado.nome }} ({{ dadosSumario.candidatoMaisVotado.votos }} votos)</p>
        </div>
      </section>

      <section class="details-section">
        <div class="tabela-resultados">
          <h2>Resultados por Bairro</h2>
          <table>
            <thead>
              <tr>
                <th>Bairro</th>
                <th>Candidato</th>
                <th>Partido</th>
                <th>Votos</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(resultado, index) in resultadosDetalhados" :key="index">
                <td>{{ resultado.bairro }}</td>
                <td>{{ resultado.candidato }}</td>
                <td>{{ resultado.partido }}</td>
                <td>{{ resultado.votos }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="grafico-votos">
          <h2>Distribuição de Votos por Candidato</h2>
          <Bar v-if="chartData.datasets[0].data.length > 0" :data="chartData" :options="chartOptions" />
        </div>
      </section>
    </div>
     <div v-if="!loading && !filtroMunicipio" class="placeholder">
        <p>Por favor, selecione um município e ano para ver os resultados.</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';
import { Bar } from 'vue-chartjs';
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js';

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale);

// --- Estado Reativo ---
const loading = ref(false);
const municipios = ref([]);
const filtroMunicipio = ref(null);
const filtroAno = ref(2024);

const dadosSumario = ref(null);
const resultadosDetalhados = ref([]);

// --- Carregamento de Dados ---
onMounted(async () => {
  // Dados Fictícios para teste (MOCK)
  // Remova ou comente esta parte quando sua API estiver pronta
  municipios.value = [
    { id: 1, nome: 'Aracaju' },
    { id: 2, nome: 'Nossa Senhora do Socorro' }
  ];
  // Fim dos dados fictícios

  // Carrega a lista de municípios para o filtro (descomente para usar a API)
  /*
  try {
    const response = await axios.get('/api/locais?tipo=municipio'); 
    municipios.value = response.data;
    if (municipios.value.length > 0) {
      filtroMunicipio.value = municipios.value[0].id; // Seleciona o primeiro por padrão
      fetchData();
    }
  } catch (error) {
    console.error("Erro ao buscar municípios:", error);
  }
  */
});

async function fetchData() {
  if (!filtroMunicipio.value || !filtroAno.value) return;

  loading.value = true;
  // Simula um delay da API
  await new Promise(resolve => setTimeout(resolve, 1000)); 

  // Dados Fictícios para teste (MOCK)
  // Substitua este bloco pela chamada real à API
  try {
    dadosSumario.value = {
      totalVotosApurados: 12530,
      totalBairrosMapeados: 8,
      candidatoMaisVotado: {
        nome: 'Candidato A',
        votos: 4890
      }
    };

    resultadosDetalhados.value = [
      { bairro: 'Jardins', candidato: 'Candidato A', partido: 'Partido X', votos: 1500 },
      { bairro: 'Jardins', candidato: 'Candidato B', partido: 'Partido Y', votos: 850 },
      { bairro: 'Centro', candidato: 'Candidato A', partido: 'Partido X', votos: 2100 },
      { bairro: 'Centro', candidato: 'Candidato B', partido: 'Partido Y', votos: 1200 },
      { bairro: 'Siqueira Campos', candidato: 'Candidato A', partido: 'Partido X', votos: 1290 },
      { bairro: 'Siqueira Campos', candidato: 'Candidato C', partido: 'Partido Z', votos: 1800 },
    ];
  } catch (error) {
     console.error("Erro ao buscar dados das eleições:", error);
  } finally {
     loading.value = false;
  }
  // Fim do MOCK

  // Chamada real à API (descomente para usar)
  /*
  try {
    const [resSumario, resResultados] = await Promise.all([
      axios.get(`/api/eleicoes/sumario?municipio_id=${filtroMunicipio.value}&ano=${filtroAno.value}`),
      axios.get(`/api/eleicoes/resultados?municipio_id=${filtroMunicipio.value}&ano=${filtroAno.value}`)
    ]);
    
    dadosSumario.value = resSumario.data;
    resultadosDetalhados.value = resResultados.data;

  } catch (error) {
    console.error("Erro ao buscar dados das eleições:", error);
  } finally {
    loading.value = false;
  }
  */
}
</script>

<style scoped>
.dashboard-eleicoes {
  /* push everything a bit lower on the page (adjusted offset) */
  padding-top: 222px; /* reduced 50px to match requested tweak */
  padding-left: 1rem;
  padding-right: 1rem;
  box-sizing: border-box;
}

.dashboard-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  margin-bottom: 2rem;
}

.dashboard-header h1 {
  font-size: 2.2rem; /* larger title */
  margin: 0;
}

.filtros select {
  font-size: 1.05rem; /* larger selects */
  padding: 0.6rem 0.9rem;
  margin-left: 0.5rem;
  border-radius: 6px;
}

.loading {
  font-size: 1.15rem;
  text-align: center;
  margin-top: 3rem;
}

.dashboard-content {
  /* give content more breathing room and push visually down */
  margin-top: 1.5rem;
}

.summary-cards {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
}

.summary-cards .card {
  flex: 1 1 0;
  padding: 1.25rem;
  border-radius: 10px;
  background: #fff;
  box-shadow: 0 6px 18px rgba(0,0,0,0.06);
}

.summary-cards .card h3 {
  font-size: 1.05rem;
  margin-bottom: 0.6rem;
}

.summary-cards .card p {
  font-size: 1.4rem; /* bigger numbers */
  font-weight: 600;
  margin: 0;
}

.details-section {
  display: flex;
  gap: 1.5rem;
  align-items: flex-start;
}

.tabela-resultados {
  flex: 1 1 55%;
  overflow: auto;
}

.tabela-resultados table {
  width: 100%;
  border-collapse: collapse;
}

.tabela-resultados th,
.tabela-resultados td {
  padding: 0.9rem 0.8rem; /* larger cells */
  text-align: left;
  border-bottom: 1px solid #e6e6e6;
  font-size: 1.05rem;
}

.grafico-votos {
  flex: 1 1 45%;
  min-width: 320px;
}

.grafico-votos h2 {
  font-size: 1.1rem;
  margin-bottom: 0.8rem;
}

/* Responsive: on small screens stack and keep increased sizing */
@media (max-width: 900px) {
  .dashboard-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .details-section {
    flex-direction: column;
  }

  .summary-cards {
    flex-direction: column;
  }

  .dashboard-eleicoes {
    padding-top: 3.5rem;
  }
}
</style>