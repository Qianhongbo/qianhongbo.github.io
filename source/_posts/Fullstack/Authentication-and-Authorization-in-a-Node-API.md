---
title: Authentication and Authorization in a Node API
date: 2022-05-20 11:07:15
categories: 
- Fullstack
---

## Hash and Salt

- A hashed password has been run through a function that generates a long encrypted string from the original password.
- The same password run through the same hash function will generate the same response, this is how we can match passwords when users log in. But from the result, we can't get the password back.
- Simply hashing passwords though isn't enough, adding **Salt**, an extra string sequence to the beginning or end of a password before hashing it makes it much harder for attackers to decrypt passwords

## Process

- Add the dependency: `npm i bcrypt`
- Import bcrypt into the user model `import bcrypt from 'bcrypt'`
- Create some environment variable in the local 
  - `BCRYPT_PASSWORD`=some string
  - `SALT_ROUNDS`=10
- Use the hash method inside the create method to hash, salt and pepper the password and save the resulting value to the password column on the users table

```ts
const pepper = process.env.BCRYPT_PASSWORD;
const saltRounds = process.env.SALT_ROUNDS as string;
const hash = bcrypt.hashSync(
  u.password + pepper,
  parseInt(saltRounds)
)
const result = await conn.query(sql, [u.username, hash])
```

- Add an authentication route to implement this job

```tsx
async authenticate(username: string, password: string): Promise<User | null> {
    // @ts-ignore
    const conn = await Client.connect();
    const sql = 'SELECT password FROM users where username=($1)';
    const result = await conn.query(sql, [username]);
    const pepper = process.env.BCRYPT_PASSWORD;
    console.log(password + pepper);

    if (result.rows.length) {
      const user = result.rows[0];
      console.log(user);
      if (bcrypt.compareSync(password + pepper, user.password)) {
        return user;
      }
    }
    return null;
 }
```

## JSON Web Tokens (JWT)

### Structure

- header
- payload
- signature

### Details

#### header

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

#### payload

Used for indentify which user this request belongs to.

Note: Don't put any secret information to this part.

```json
{
  "user": "Maverick",
  "school": "UCSD"
}
```



