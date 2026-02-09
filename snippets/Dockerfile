# 1. Use Python 3.13 bookworm as the base
FROM ghcr.io/astral-sh/uv:python3.13-bookworm

# 2. Set the working directory
WORKDIR /app

# 3. Set environment variables for UV
# UV_COMPILE_BYTECODE: Compiles python files to .pyc for faster startup
# UV_LINK_MODE: copy (safer for docker layers than hardlinks)
ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy

# 4. create a non-root user for security
RUN useradd -u 1000 app

# 5. Set HOME explicitly to /app
# This prevents the "/nonexistent/.cache/uv" error by telling tools 
# to look for their config/cache in /app instead of /nonexistent
ENV HOME=/app

# 6. Ensures the /app directory (and everything inside) is owned by the app user
# We run this BEFORE switching users
RUN chown -R app:app /app

# 7. Copy pyproject.toml and uv.lock with correct ownership
# changing ownership during COPY is more efficient than running chown later
COPY --chown=app:app pyproject.toml uv.lock ./

# 8. Switch to the non-root user BEFORE installing dependencies
USER app

# 9. Install dependencies
# Since we are now the 'app' user, the .venv will be owned by 'app'
# and the cache will be written to /app/.cache/uv.
RUN uv sync --frozen --no-install-project --no-dev


# 10. Copy the rest of the application code
COPY --chown=app:app . .
RUN uv sync --frozen --no-dev

# 11. Expose the port
EXPOSE 8000


# 12. Command to run the application
# For production: CMD ["uv", "run", "fastapi", "run", "main.py" ...
# This assumes your main.py is in root, I usually have mine in src/main.py
# so change it accordingly
CMD ["uv", "run", "fastapi", "dev", "main.py", "--host", "0.0.0.0", "--port", "8000"]