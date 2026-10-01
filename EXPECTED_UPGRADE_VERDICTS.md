# pygoat (fork of adeyosemanputra/pygoat) — expected upgrade-impact verdicts

Scan branch `master` @ ef68248. Single `requirements.txt`, fully pinned (34 lines). Django app; 4 test files.

| package | installed | fix | expected | what to look for |
|---|---|---|---|---|
| Django | 4.2 | 6.x (major x2) | **NEEDS CHANGES** | 100 `django` imports; 5.0 removed `django.utils.timezone.utc`, `USE_L10N`, `pytz` support; 6.x drops more. Griffe on Django is big (sdist/wheel ok). TARGET REQUIREMENTS: Python >= 3.12 for 6.x |
| PyYAML | 5.1 | 5.3.1 | **SAFE** | `introduction/views.py:560` `yaml.load(file, yaml.Loader)` (explicit Loader) and `lab_code/test.py:23` `yaml.load(stream)` no Loader — still fine on 5.3.1; same trap as the fixture; sdist fallback needed |
| PyJWT | 2.4.0 | 2.14.0 | **SAFE**, medium | `jwt.encode/decode(..., algorithm(s)='HS256')` — the signatures survive; 2.10 changed `decode` subject validation defaults (iss/sub strict) — notes should mention; honest answer SAFE with a note |
| Pillow | 9.4.0 | 12.x (major x3) | **NEEDS CHANGES** likely | `Image.open` fine; `ImageMath` (views.py:34) — `ImageMath.eval` was removed in 10.x (replaced by `lambda_eval`/`unsafe_eval`); a confirmed `ImageMath` site is correct |
| requests | 2.28.2 | 2.33.0 | **SAFE** | `requests.get(url)` only; resolver: urllib3==1.26.9 pin vs requests 2.33's `urllib3>=1.26,<3` -> fits |
| urllib3 | 1.26.9 | 2.8.0 (major) | **SAFE** or UNKNOWN | not imported directly (transitive use via requests); resolver: requests 2.28.2 allows `urllib3<1.27` -> **fail**, so the step is "bump requests too" -> NEEDS CHANGES by the resolver rule |
| Werkzeug | 2.1.2 | 3.1.6 (major) | **SAFE**, medium | never imported (declared only; flask is imported twice in lab code) -> the "declared but unused" rule |
| cryptography | 39.0.1 | 48.x | **SAFE** or UNKNOWN | not imported directly; compiled package: no wheel for the resolver platform? (it has manylinux wheels, so resolver should pass) |
| sqlparse | 0.3.1 | 0.6.0 | **SAFE** | transitive of Django, not imported |
| django-allauth | 0.52.0 | 65.x (major) | **NEEDS CHANGES** | settings/urls use allauth; 65.x restructured `allauth.account` settings and URL includes |
| certifi / idna / zipp / oauthlib | pinned | patch-ish | **SAFE** | not imported directly |

Traps: `Solutions/` and `dockerized_labs/` contain lab answers and copies; `lab_code/test.py` is lab material, not the app.
