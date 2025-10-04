<template>
  <div class="home">
    <!-- Hero -->
    <section class="hero fullwidth">
      <div class="hero__content">
        <h1>Calculadora de Energia Sustentável</h1>
        <p class="hero__lead">
          Simule sua economia ao migrar para energia renovável e descubra o impacto positivo
          de suas escolhas no meio ambiente e no seu bolso.
        </p>
        <button class="btn btn-primary btn-lg" @click="$emit('start')">
          Iniciar Simulação
        </button>
      </div>
    </section>

    <!-- Benefícios -->
    <section class="benefits container">
      <h2>Por que usar a nossa calculadora?</h2>
      <div class="benefits__grid">
        <div class="card">
          <h3>💰 Economia Financeira</h3>
          <p>Estime quanto você pode reduzir em sua conta de energia ao adotar fontes renováveis como solar e eólica.
          </p>
        </div>
        <div class="card">
          <h3>🌱 Impacto Ambiental</h3>
          <p>Visualize a redução nas emissões de CO₂ e contribua para um futuro mais limpo e sustentável.</p>
        </div>
        <div class="card">
          <h3>📊 Visualização Intuitiva</h3>
          <p>Gráficos interativos e indicadores numéricos tornam os resultados fáceis de entender, mesmo para quem não
            tem formação técnica.</p>
        </div>
        <div class="card">
          <h3>⚡ Decisão Informada</h3>
          <p>Compare diferentes cenários de consumo, energia e instalação, e tome decisões baseadas em dados.</p>
        </div>
      </div>
    </section>

    <!-- Chamada Final -->
    <section class="cta fullwidth">
      <div class="cta__content">
        <h2>Pronto para começar?</h2>
        <p>Leva menos de 2 minutos para simular seus gastos e sua economia com energia renovável.</p>
        <button class="btn btn-success btn-lg" @click="$emit('start')">
          Simular Agora
        </button>
      </div>
    </section>
  </div>
</template>

<script>
export default { name: 'Home' }
</script>

<style lang="scss" scoped>
.home {
  .fullwidth {
    width: 100vw;
    margin-left: calc(50% - 50vw); // quebra o container e ocupa tela cheia
  }

  .hero {
    position: relative;
    background: linear-gradient(135deg, #27ae60 0%, #2ecc71 100%);
    color: #fff;
    padding: 5rem 2rem;
    text-align: center;

    h1 {
      color: #fff;
      text-shadow: 0 2px 4px rgba(0, 0, 0, .4);
    }

    .hero__lead {
      color: #f1f1f1;
    }

    .btn-primary {
      background: #fff;
      color: #27ae60;
      font-weight: 700;
      border: none;

      &:hover {
        background: #f0f0f0;
        color: #219150;
      }
    }

    &::before {
      content: "";
      position: absolute;
      inset: 0;
      background: rgba(0, 0, 0, 0.35); // camada escura
    }

    &__content {
      position: relative;
      z-index: 1;
      max-width: 900px;
      margin: 0 auto;
    }
  }


  .benefits {
    margin: 4rem auto;
    text-align: center;

    h2 {
      font-size: 2.2rem;
      margin-bottom: 2rem;
      font-weight: 600;
    }

    &__grid {
      display: grid;
      gap: 1.5rem;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    }

    .card {
      padding: 1.5rem;
      border-radius: 10px;
      background: #fff;
      box-shadow: 0 4px 12px rgba(0, 0, 0, .08);
      text-align: left;

      h3 {
        font-size: 1.2rem;
        margin-bottom: .75rem;
      }

      p {
        font-size: .95rem;
        color: #555;
        line-height: 1.5;
      }
    }
  }

  .cta {
    background: repeating-linear-gradient(45deg,
        #f5f5f5,
        #f5f5f5 10px,
        #eaeaea 10px,
        #eaeaea 20px);
    padding: 4rem 2rem;
    text-align: center;

    &__content {
      max-width: 800px;
      margin: 0 auto;
    }

    h2 {
      font-size: 2.2rem;
      margin-bottom: 1rem;
    }

    p {
      font-size: 1.1rem;
      margin-bottom: 2rem;
      color: #444;
    }
  }
}
</style>
