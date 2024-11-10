<template>
  <div id="password-strength-container">
    <p class="title text">Create a password</p>
    <input type="text" :class="['field', {error: isUserNameEmpty}]" v-model="username" @keydown.enter="checkPasswordStrength" placeholder="Username">
    <p v-if="isUserNameEmpty" class="helper-text text bad">Username cannot be empty</p>
    <div class="password-field-container">
      <input :type="showPassword ? 'text' : 'password'" v-model="password" @keydown.enter="checkPasswordStrength" placeholder="Password"
             :class="['field', {error: Object.values(this.passwordRequirements).filter(req => req.status === false).length > 0}]">
      <button class="show-password-button text" @click="togglePasswordVisibility">
        {{ showPassword ? "hide" : "show" }}
      </button>
    </div>
    <p class="helper-text text bad">{{ passwordHelperText }}</p>
    <div class="password-strength-review-container" v-if="passwordStrength !== null">
      <p class="password-strength-text text">
        Password status:
        <span :class="{bad: passwordStrength <= 4, medium: passwordStrength > 4 && passwordStrength < 7, good: passwordStrength === 7}">
        {{ passwordRank }}
      </span>
      </p>
    </div>
    <button class="check-button text" @click="checkPasswordStrength">
      Check Password Strength
    </button>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      username: '',
      isUserNameEmpty: null,
      showPassword: false,
      password: '',
      passwordStrength: null,
      passwordRank: null,
      passwordRequirements: {
        hasEnoughCharacters: {
          status: null,
          message: "At least 8 characters long"
        },
        hasLowerCaseChar: {
          status: null,
          message: "One lower-case character (a-z)"
        },
        hasUpperCaseChar: {
          status: null,
          message: "One upper-case character (A-Z)"
        },
        hasDigit: {
          status: null,
          message: "One digit (0-9)"
        },
        hasSpecialChar: {
          status: null,
          message: "One special character (!-?)"
        },
        isNotCommon: {
          status: null,
          message: "Password is not common"
        },
        isNotUsername: {
          status: null,
          message: "Is not same as username"
        }
      },
      commonPasswords: [
        "123456",
        "12345678",
        "password12345",
        "123456789",
        "12345",
        "12345678",
        "123123",
      ],
      passwordHelperText: ''
    }
  },
  methods: {
    togglePasswordVisibility() {
      this.showPassword = !this.showPassword;
    },
    checkPasswordStrength() {
      this.isUserNameEmpty = this.username.length === 0;
      this.passwordRequirements.hasLowerCaseChar.status = /[a-z]/.test(this.password);
      this.passwordRequirements.hasUpperCaseChar.status = /[A-Z]/.test(this.password);
      this.passwordRequirements.hasDigit.status = /\d/.test(this.password);
      this.passwordRequirements.hasSpecialChar.status = /[!@#$%^&*(),.?":{}|<>]/.test(this.password);
      this.passwordRequirements.isNotCommon.status = !this.commonPasswords.includes(this.password);
      this.passwordRequirements.hasEnoughCharacters.status = this.password.length > 7;
      this.passwordRequirements.isNotUsername.status = this.password !== this.username;

      this.passwordStrength = 0;

      if (!this.passwordRequirements.hasEnoughCharacters.status) {
        this.passwordRank = "Too short";
      } else if (!this.passwordRequirements.isNotUsername.status){
        this.passwordRank = "Same as Username";
      } else if (!this.passwordRequirements.isNotCommon.status) {
        this.passwordRank = "Too Common";
      }
      else {
        Object.values(this.passwordRequirements).forEach(attribute => {
          if (attribute.status)
            this.passwordStrength++;
        })
        this.updatePasswordRank();
      }
      this.updatePasswordHelperText();
    },
    updatePasswordRank() {
      if (this.passwordStrength <= 3) {
        this.passwordRank = "Very Bad"
      } else if (this.passwordStrength <= 4) {
        this.passwordRank = "Bad"
      } else if (this.passwordStrength <= 6) {
        this.passwordRank = "Okay"
      } else {
        this.passwordRank = "Strong"
      }
    },
    updatePasswordHelperText() {
      this.passwordHelperText = '';
      Object.values(this.passwordRequirements).forEach((requirement) => {
        if (!requirement.status) {
          this.passwordHelperText += requirement.message + ', '
        }
      })
      this.passwordHelperText = this.passwordHelperText.substring(0, this.passwordHelperText.length - 2)
    }
  }
}
</script>

<style>
body {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  background-color: darkgreen;
}

#password-strength-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: white;
  padding: 2rem;
  border-radius: 1rem;
  width: 28rem;
  text-overflow: clip;

  .title {
    margin-top: 0;
    margin-bottom: 1rem;
    font-size: 3rem;
    color: darkgreen;
  }

  .password-field-container {
    position: relative;
    margin-top: 1.5rem;
  }

  .field {
    height: 2rem;
    width: 19rem;
    font-size: 1.25rem;
    padding: 0.5rem 4rem 0.5rem 0.75rem;
  }

  .show-password-button {
    position: absolute;
    cursor: pointer;
    top: 1rem;
    right: 0.75rem;
  }

  .password-strength-review-container {
    padding: 0 0.5rem;
  }

  .password-strength-text {
    font-size: 1.25rem;
  }

  .check-button {
    margin-top: 1.5rem;
    height: 3rem;
    cursor: pointer;
    font-size: 1rem;
  }

  .good {
    color: darkgreen;
  }

  .bad {
    color: red;
  }

  .medium {
    color: orange;
  }

  .error {
    border-color: red;
  }

  .text {
    font-family: Alef, sans-serif;
  }

  .helper-text {
    width: 23rem;
    margin-top: 0.5rem;
    text-align: start;
    font-size: 0.75rem;
  }
}
</style>