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
  municipios.value = [
    { id: 1, nome: 'Aracaju' },
    { id: 2, nome: 'Nossa Senhora do Socorro' }
  ];
  // Fim dos dados fictícios
});

async function fetchData() {
  if (!filtroMunicipio.value || !filtroAno.value) return;

  loading.value = true;
  // Simula um delay da API
  await new Promise(resolve => setTimeout(resolve, 1000));

  // Dados Fictícios para teste (MOCK)
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
}

// --- Dados para o Gráfico (Propriedade Computada) ---
const chartData = computed(() => {
  const votosPorCandidato = {};

  resultadosDetalhados.value.forEach(res => {
    if (!votosPorCandidato[res.candidato]) {
      votosPorCandidato[res.candidato] = 0;
    }
    votosPorCandidato[res.candidato] += res.votos;
  });

  return {
    labels: Object.keys(votosPorCandidato),
    datasets: [
      {
        label: 'Total de Votos',
        backgroundColor: '#42A5F5',
        data: Object.values(votosPorCandidato)
      }
    ]
  };
});

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false
};

</script>

<style scoped>
.dashboard-eleicoes {
  padding-top: 90px;
  padding-left: 1rem;
  padding-right: 1rem;
  box-sizing: border-box;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  color: #333;
  min-height: 100vh;
  background: #f6f8fa;
}

.dashboard-header {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  gap: 1.5rem;
  margin-bottom: 2.5rem;
  margin-top: 0;
  padding-top: 0;
}

.dashboard-header h1 {
  font-size: 2.2rem;
  margin: 0;
  flex: 1 1 100%;
  padding-top: 0;
}

.filtros {
  display: flex;
  gap: 1rem;
}
.filtros select {
  font-size: 1.1rem;
  padding: 0.7rem 1.1rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  background: #fff;
}

.loading {
  font-size: 1.2rem;
  text-align: center;
  margin-top: 3rem;
}

.dashboard-content {
  margin-top: 1.5rem;
}

.summary-cards {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
  margin-bottom: 2.5rem;
}

.summary-cards .card {
  flex: 1 1 220px;
  min-width: 200px;
  padding: 1.5rem 1.2rem;
  border-radius: 12px;
  background: #fff;
  box-shadow: 0 4px 16px rgba(0,0,0,0.07);
  display: flex;
  flex-direction: column;
  align-items: center;
}
.summary-cards .card h3 {
  font-size: 1.1rem;
  margin-bottom: 0.7rem;
  color: #555;
}
.summary-cards .card p {
  font-size: 1.7rem;
  font-weight: 700;
  margin: 0;
  color: #0056b3;
}

.details-section {
  display: flex;
  flex-wrap: wrap;
  gap: 2rem;
  align-items: flex-start;
}
.tabela-resultados {
  flex: 2 1 350px;
  min-width: 300px;
  background: #fff;
  padding: 1.5rem 1rem 1.5rem 1.5rem;
  border-radius: 10px;
  box-shadow: 0 4px 16px rgba(0,0,0,0.07);
  overflow-x: auto;
}
.grafico-votos {
  flex: 1 1 320px;
  min-width: 260px;
  max-width: 500px;
  max-height: 420px;
  height: 420px;
  background: #fff;
  padding: 1.5rem 1.2rem;
  border-radius: 10px;
  box-shadow: 0 4px 16px rgba(0,0,0,0.07);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  overflow: hidden;
}
.grafico-votos h2 {
  font-size: 1.1rem;
  margin-bottom: 0.8rem;
}

h2 {
  margin-top: 0;
  border-bottom: 1px solid #eee;
  padding-bottom: 0.5rem;
  margin-bottom: 1rem;
}

table {
  width: 100%;
  border-collapse: collapse;
  background: #fff;
}
th, td {
  padding: 0.95rem 0.8rem;
  text-align: left;
  border-bottom: 1px solid #f0f0f0;
  font-size: 1.08rem;
}
th {
  background-color: #f7f7f7;
}
tbody tr:hover {
  background-color: #f1f8ff;
}

.placeholder {
  text-align: center;
  font-size: 1.2rem;
  color: #888;
  margin-top: 5rem;
}

@media (max-width: 1100px) {
  .details-section {
    flex-direction: column;
  }
  .grafico-votos, .tabela-resultados {
    min-width: 0;
    width: 100%;
    max-width: 100%;
    height: auto;
    max-height: 420px;
  }
}
@media (max-width: 700px) {
  .dashboard-eleicoes {
    padding-top: 90px;
  }
  .dashboard-header h1 {
    font-size: 1.3rem;
  }
  .summary-cards {
    flex-direction: column;
    gap: 1rem;
  }
  .details-section {
    gap: 1rem;
  }
  .grafico-votos, .tabela-resultados {
    padding: 1rem 0.5rem;
    max-height: 320px;
  }
}
</style>