# A Massive Problem

## ANALYZE

<figure><img src="../../.gitbook/assets/image (759).png" alt=""><figcaption></figcaption></figure>

Based on the challenge description, my initial suspicion was that the vulnerability likely resided in the authorization logic, as it was fairly obvious that the developer had carelessly skipped a proper recheck and deployed it straight to production.



Upon accessing the challenge, we are presented with a login form

<figure><img src="../../.gitbook/assets/image (758).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (760).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (761).png" alt=""><figcaption></figcaption></figure>

After logging in, the only functionality available is the ability to update personal profile information.

### src analyze

Next, we will analyze the source code

```python
@app.route('/api/register', methods=['POST'])
def register():
    incoming = request.get_json(silent=True) or request.form.to_dict()
    username = incoming.get('username', '').strip()
    password = incoming.get('password', '')
    full_name = incoming.get('full_name', '').strip()
    title = incoming.get('title', '').strip()
    team = incoming.get('team', '').strip()
    if not username or not password or not full_name or not title or not team:
        return jsonify({'error': 'Please complete all required fields.'}), 400
    if not valid_password(password):
        return jsonify({'error': 'Password does not meet policy.'}), 400
    record = {
        'username': username,
        'password': password,
        'role': 'standard',
        'full_name': full_name,
        'title': title,
        'team': team
    }
    record.update(incoming)
    if not record.get('username') or not record.get('password') or not record.get('role'):
        return jsonify({'error': 'Unable to create account.'}), 400
    conn = get_db()
    conn.execute(
        'insert into users (username, password, role, full_name, title, team) values (?, ?, ?, ?, ?, ?) '
        'on conflict(username) do update set password=excluded.password, role=excluded.role, full_name=excluded.full_name, title=excluded.title, team=excluded.team',
        (record['username'], record['password'], record['role'], record['full_name'], record['title'], record['team'])
    )
    conn.commit()
    conn.close()
    session['auth_notice'] = {
        'title': 'Account created',
        'message': 'Your workspace account is ready. Sign in to continue.'
    }
    return jsonify({'redirect': url_for('login_page')})
```

* Accepts input from JSON body or form data.
* Reads these required fields:
  * `username`
  * `password`
  * `full_name`
  * `title`
  * `team`
* Rejects the request if any required field is missing.
* Validates the password against the system password policy.
* Builds a user record with a default role of `standard`.
* Saves the user into the `users` table.
  * If the username already exists, the record is updated with the new values.
* Stores a session notice indicating the account was created.
* Returns a JSON response containing a redirect to the login page.



<figure><img src="../../.gitbook/assets/image (763).png" alt=""><figcaption></figcaption></figure>

```python
@app.route('/admin')
def admin():
    if 'username' not in session:
        return redirect(url_for('login_page'))
    if session.get('role') != 'admin':
        return redirect(url_for('dashboard'))
    return render_template('admin.html', username=session.get('username'), flag=os.getenv('FLAG', 'CIT{test_flag}'))
```

And to obtain the flag, the account must have the `admin` role. Otherwise, it will not be authorized to access `/admin`



### hypothesis

Looking at the `/api/register` endpoint, we can immediately spot a critical flaw in the account creation logic: although the default role is `standard`, the implementation raises an important question.

What would happen if an attacker injected an additional key-value pair such as `'role': 'admin'` during registration?

### Verifying the hypothesis

<figure><img src="../../.gitbook/assets/image (764).png" alt=""><figcaption></figcaption></figure>



## EXPLOIT



<figure><img src="../../.gitbook/assets/image (765).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (766).png" alt=""><figcaption></figcaption></figure>

## FLAG

```
CIT{M@ss_@ssignm3nt_Pr1v3sc}
```
