# Scratch tooling image (NOT for merge): regenerates uv.lock for the
# wandb==0.29.0 bump in the same environment as the repository's
# "Generate UV Lockfile" workflow (NGC pytorch 25.06 + uv 0.7.2 +
# `uv lock --no-build-isolation`). Prints the lockfile into the build
# log between LOCK_B64_BEGIN/LOCK_B64_END markers (gzip+base64).
FROM nvcr.io/nvidia/pytorch:25.06-py3
RUN pip install --no-cache-dir uv==0.7.2
WORKDIR /workspace
COPY . .
RUN uv venv /opt/venv --system-site-packages
ENV UV_PROJECT_ENVIRONMENT=/opt/venv \
    VIRTUAL_ENV=/opt/venv \
    UV_LINK_MODE=copy
ENV PATH="/opt/venv/bin:$PATH"
RUN uv pip install setuptools wheel wheel_stub pybind11 "Cython>=3.0.0" ninja packaging
RUN sed -i 's|schemecloak://|http://|g' tests/unit_tests/deploy/test_image_url_validator.py && uv lock --no-build-isolation
RUN echo "LOCK_SHA256 $(sha256sum uv.lock)" \
 && echo LOCK_B64_BEGIN \
 && gzip -c uv.lock | base64 -w 76 \
 && echo LOCK_B64_END
