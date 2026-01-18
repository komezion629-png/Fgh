<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>DBAR Survey — Survey</title>
  <link rel="stylesheet" href="styles.css" />
</head>
<body class="survey">
  <header class="site-header">
    <div class="container header-inner">
      <a class="brand" href="index.html">DBAR Survey</a>
      <nav>
        <a class="btn btn-outline" href="index.html">Home</a>
      </nav>
    </div>
  </header>

  <main class="container survey-main" role="main">
    <section class="survey-card" aria-labelledby="survey-heading">
      <h2 id="survey-heading">DBAR Quick Survey</h2>
      <p class="muted">We appreciate your time — this should take less than 2 minutes.</p>

      <form id="surveyForm" action="#" method="POST" onsubmit="handleSubmit(event)">
        <label for="name">Full name</label>
        <input id="name" name="name" type="text" required placeholder="Your name" />

        <label for="email">Email (optional)</label>
        <input id="email" name="email" type="email" placeholder="you@example.com" />

        <label for="experience">How satisfied are you with DBAR?</label>
        <select id="experience" name="experience" required>
          <option value="">Choose...</option>
          <option value="5">Very satisfied</option>
          <option value="4">Satisfied</option>
          <option value="3">Neutral</option>
          <option value="2">Dissatisfied</option>
          <option value="1">Very dissatisfied</option>
        </select>

        <fieldset>
          <legend>Which features matter most to you? (select all that apply)</legend>
          <label><input type="checkbox" name="features" value="reports" /> Reports</label>
          <label><input type="checkbox" name="features" value="dashboards" /> Dashboards</label>
          <label><input type="checkbox" name="features" value="export" /> Exporting data</label>
        </fieldset>

        <label for="comments">Comments (optional)</label>
        <textarea id="comments" name="comments" rows="4" placeholder="Anything else to share?"></textarea>

        <div class="form-actions">
          <button type="submit" class="btn btn-primary">Submit Survey</button>
          <button type="reset" class="btn btn-outline">Reset</button>
        </div>

        <p id="status" class="muted" aria-live="polite" style="margin-top:0.5rem"></p>
      </form>
    </section>
  </main>

  <footer class="site-footer">
    <div class="container">
      <small>&copy; <span id="year2"></span> DBAR Survey</small>
    </div>
  </footer>

  <script>
    document.getElementById('year2').textContent = new Date().getFullYear();

    function handleSubmit(e) {
      e.preventDefault();
      const status = document.getElementById('status');
      status.textContent = 'Submitting...';

      // This is a placeholder. Replace with real POST to your server.
      setTimeout(() => {
        status.textContent = 'Thanks — your responses have been recorded.';
        e.target.reset();
      }, 800);
    }
  </script>
</body>
</html>
