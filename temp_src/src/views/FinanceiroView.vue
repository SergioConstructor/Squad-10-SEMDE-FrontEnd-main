<style scoped>
/* ============================================= */
/* ESTILO DA PÁGINA DE ELEIÇÕES         */
/* Adaptado com base no design da tela        */
/* de Centro Financeiro                 */
/* ============================================= */

/* --- Layout Principal --- */
.dashboard-eleicoes {
  min-height: calc(100vh - 96px); /* Altura igual à da pág. Financeiro */
  background: linear-gradient(180deg, #f4f7ff 0%, #ffffff 100%); /* Fundo gradiente */
  display: flex;
  flex-direction: column;
  gap: 32px;
  padding: clamp(24px, 4vw, 48px); /* Padding responsivo */
  font-family: "Poppins", sans-serif; /* Fonte padrão do projeto */
}

/* --- Cabeçalho e Filtros --- */
.dashboard-header {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
  padding-bottom: 24px;
  border-bottom: 1px solid rgba(22, 42, 74, 0.08); /* Borda sutil como na tabela */
}

.dashboard-header h1 {
  margin: 0;
  font-size: clamp(26px, 4vw, 32px);
  font-weight: 700;
  color: #102140; /* Cor dos títulos principais */
}

.filtros {
  display: flex;
  gap: 16px;
}

.filtros select {
  /* Estilo dos inputs e selects da pág. Financeiro */
  border: 1px solid rgba(96, 116, 153, 0.22);
  border-radius: 14px;
  padding: 12px 16px;
  font-size: 15px;
  background: #f7f9ff;
  font-family: "Poppins", sans-serif;
  color: #4a5872;
  transition: border-color 0.2s ease, box-shadow 0.2s ease, background 0.2s ease;
}

.filtros select:focus {
  border-color: #0f62cc;
  background: #ffffff;
  box-shadow: 0 0 0 3px rgba(15, 98, 204, 0.12);
}
</style>