# ==================================================
# ISRO GEO-INTELLIX: PRODUCTION DOCKERFILE
# ==================================================

# STEP 1: Use the correct NVIDIA CUDA Base
FROM nvidia/cuda:11.8.0-devel-ubuntu22.04

# STEP 2: Install System Dependencies
# Added 'libgl1' which was causing the CV2 error
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    python3.10 python3.10-distutils python3-pip \
    git build-essential libsm6 libxext6 libxrender-dev libgl1 \
    && update-alternatives --install /usr/bin/python python /usr/bin/python3.10 1 \
    && rm -rf /var/lib/apt/lists/*

# STEP 3: Setup Workspace
WORKDIR /app/GeoChat
COPY requirements.txt .
COPY . .

# STEP 4: Python Dependency Management (The "Fixes")

# 4a. PRE-EMPTIVE STRIKE: Pin NumPy to 1.x
# This prevents the "Module compiled with NumPy 1.x" crash.
RUN pip install "numpy<2.0"

# 4b. Install Core AI Frameworks
RUN pip install --no-cache-dir torch==2.0.1 torchvision==0.15.2 deepspeed==0.9.5

# 4c. Install Repo Requirements
# We removed '--no-deps' to ensure we get sub-dependencies like pandas/matplotlib
RUN pip install --no-cache-dir -r requirements.txt --upgrade

# 4d. THE "MISSING LINK" INSTALLATION
# This line installs every single package that crashed your demo today.
# - bitsandbytes: For 4-bit loading on your 4050 GPU
# - opencv-python-headless: Fixes 'No module named cv2'
# - scipy, joblib, threadpoolctl: Fixes scikit-learn errors
# - huggingface_hub: Fixes download errors
# - starlette, uvicorn, annotated-doc: Fixes Gradio/FastAPI crashes
# - linkify-it-py, mdit-py-plugins: Fixes UI display errors
RUN pip install --no-cache-dir \
    bitsandbytes \
    "opencv-python-headless<4.10" \
    scipy joblib threadpoolctl \
    huggingface_hub \
    "starlette<0.50.0" annotated-doc uvicorn \
    linkify-it-py "mdit-py-plugins<=0.3.3"

# 4e. Install GeoChat
RUN pip install .

# STEP 5: Runtime Configuration
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64/

# STEP 6: Automatic Launch Command
# This assumes you want to run the API or Demo by default.
# We use a shell script entrypoint to handle the arguments cleanly.
CMD ["python", "geochat_demo.py", "--model-path", "geochat-7B", "--load-4bit"]