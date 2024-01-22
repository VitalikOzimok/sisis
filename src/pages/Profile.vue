<script setup></script>

<template>
  <body>
    <header>
      <form method="post" name="myForm">
        <h1>Авторизация</h1>
        <table>
          <tr>
            <td><p>Логин:</p></td>
            <td><input type="text" name="login" /></td>
          </tr>
          <tr>
            <td><p>Пароль:</p></td>
            <td><input type="password" name="pass" /></td>
          </tr>
        </table>
        <tr>
          <td>
            <input type="submit" value="Войти" name="enter" />
            <input type="submit" value="Регистрация" name="enter" />
          </td>
        </tr>
      </form>
    </header>
  </body>
</template>
