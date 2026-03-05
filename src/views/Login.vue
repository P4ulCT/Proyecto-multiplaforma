<template>
  <ion-page>
    <ion-header class="ion-no-border">
      <ion-toolbar>
        <ion-title>Bienvenido</ion-title>
        <ion-buttons slot="end">
          <ion-button fill="solid" color="primary" @click="router.push({ name: 'Registro'})">
            <ion-icon :icon="personAdd" slot="start"></ion-icon>
            Registrarse
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>
    
    <ion-content class="ion-padding login-content">
      <div class="logo-container">
        <ion-icon :icon="logIn" color="primary" class="logo-icon"></ion-icon>
        <h1>Iniciar Sesión</h1>
        <p class="subtitle">Ingresa tus credenciales para continuar</p>
      </div>

      <ion-card class="login-card">
        <ion-card-content>
          <ion-item lines="none" class="input-item">
            <ion-icon :icon="person" slot="start" color="medium"></ion-icon>
            <ion-input 
              label="Usuario" 
              label-placement="floating" 
              fill="outline" 
              v-model="userStore.login.username"
              placeholder="Ingresa tu usuario"
              class="custom-input"
              :class="{ 'has-error': showErrors && !userStore.login.username }"
            >
            </ion-input>
          </ion-item>
          
          <ion-text v-if="showErrors && !userStore.login.username" color="danger" class="error-text">
            <p><ion-icon :icon="alertCircle"></ion-icon> El usuario es requerido</p>
          </ion-text>

          <ion-item lines="none" class="input-item">
            <ion-icon :icon="lockClosed" slot="start" color="medium"></ion-icon>
            <ion-input 
              label="Contraseña" 
              label-placement="floating" 
              fill="outline" 
              placeholder="Ingresa tu contraseña" 
              v-model="userStore.login.password"
              :type="showPassword ? 'text' : 'password'"
              class="custom-input"
              :class="{ 'has-error': showErrors && !userStore.login.password }"
            >
            </ion-input>
            <ion-button slot="end" fill="clear" @click="togglePassword">
              <ion-icon :icon="showPassword ? eyeOff : eye" slot="icon-only"></ion-icon>
            </ion-button>
          </ion-item>
          
          <ion-text v-if="showErrors && !userStore.login.password" color="danger" class="error-text">
            <p><ion-icon :icon="alertCircle"></ion-icon> La contraseña es requerida</p>
          </ion-text>

          <div class="forgot-password">
            <ion-button fill="clear" size="small">
              ¿Olvidaste tu contraseña?
            </ion-button>
          </div>

          <ion-button 
            expand="block" 
            class="login-button"
            @click="handleLogin"
            :disabled="isLoading"
          >
            <ion-spinner v-if="isLoading" name="crescent"></ion-spinner>
            <span v-else>
              <ion-icon :icon="logIn" slot="start"></ion-icon>
              Ingresar
            </span>
          </ion-button>

          <div class="register-link">
            <p>¿No tienes una cuenta?</p>
            <ion-button fill="clear" color="primary" @click="router.push({ name: 'Registro'})">
              Regístrate aquí
            </ion-button>
          </div>
        </ion-card-content>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { 
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent, 
  IonItem, IonInput, IonButton, IonButtons, IonIcon,
  IonCard, IonCardContent, IonText, IonSpinner,
  alertController 
} from '@ionic/vue';
import { 
  person, lockClosed, logIn, personAdd, 
  eye, eyeOff, alertCircle 
} from 'ionicons/icons';
import { useUserStore } from '@/store/user';
import { useRouter } from 'vue-router';

const userStore = useUserStore();
const router = useRouter();
const showPassword = ref(false);
const isLoading = ref(false);
const showErrors = ref(false);

function togglePassword() {
  showPassword.value = !showPassword.value;
}

function handleLogin() {
  showErrors.value = true;
  
  if (!userStore.login.username || !userStore.login.password) {
    return;
  }

  isLoading.value = true;
  
  userStore.$login()
    .then(() => {
      router.push({ name: 'Seccion' });
    })
    .catch(error => {
      alertController.create({
        header: 'Error de inicio de sesión',
        message: error.response?.data?.message || 'Credenciales incorrectas',
        buttons: ['Continuar'],
      }).then(alert => alert.present());
    })
    .finally(() => {
      isLoading.value = false;
      showErrors.value = false;
    });
}
</script>

<style scoped>
.login-content {
  --background: #f5f7fa;
}

.logo-container {
  text-align: center;
  padding: 32px 16px 24px;
}

.logo-icon {
  font-size: 64px;
  margin-bottom: 16px;
  color: var(--ion-color-primary);
}

.logo-container h1 {
  font-size: 24px;
  font-weight: 700;
  margin: 8px 0;
  color: #1a1a1a;
}

.subtitle {
  color: #666;
  font-size: 14px;
  margin: 0;
}

.login-card {
  margin: 16px;
  border-radius: 16px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.input-item {
  --background: transparent;
  --padding-start: 0;
  --inner-padding-end: 0;
  margin-bottom: 8px;
}

.custom-input {
  --background: #f8f9fa;
  --border-radius: 12px;
  --padding-start: 16px;
  --padding-end: 16px;
  margin-left: 8px;
}

.custom-input.has-error {
  --border-color: var(--ion-color-danger);
  --border-width: 1px;
}

.error-text {
  font-size: 12px;
  margin-left: 52px;
  margin-bottom: 16px;
  display: flex;
  align-items: center;
  gap: 4px;
}

.error-text p {
  margin: 4px 0;
  display: flex;
  align-items: center;
  gap: 4px;
}

.forgot-password {
  text-align: right;
  margin: 8px 0 24px;
}

.login-button {
  --border-radius: 12px;
  --padding-top: 16px;
  --padding-bottom: 16px;
  margin: 0;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.register-link {
  text-align: center;
  margin-top: 24px;
}

.register-link p {
  color: #666;
  font-size: 14px;
  margin-bottom: 4px;
}

/* Animaciones */
.login-card {
  animation: slideUp 0.5s ease-out;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Responsive */
@media (min-width: 768px) {
  .login-card {
    max-width: 400px;
    margin: 16px auto;
  }
}
</style>