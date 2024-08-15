# Day 10

## Created auth/signup routes

```
router.get('/signup', (req, res) => {
    res.render('web/layouts/auth', { page: 'signup' });
})
```

```
router.post('/signup', asyncHandler(async (req, res) => {
    const { email, password, confirmPassword } = req.body;
    if (password !== confirmPassword) {
        return res.status(400).render('web/layouts/auth', { page: 'error', status: 400, message: 'Passwords do not match.' });
    }
    // const [rows] = await pool.query('SELECT * FROM users WHERE email = ?', [email]);
    const rows = await User.read(email);
    if (rows.length > 0) {
        return res.status(400).render('web/layouts/auth', { page: 'error', status: 400, message: 'User already exists. '});
    }
    const user_id = uuidv4();
    const hashedPassword = await bcrypt.hash(password, 10);
    // await pool.query('INSERT INTO users (email, password, user_id) VALUES (?, ?, ?)', [email, hashedPassword, user_id]);
    await User.add(email, hashedPassword, user_id);
    res.redirect('/');
}));
```