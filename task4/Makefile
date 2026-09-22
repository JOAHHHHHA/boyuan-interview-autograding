# 你需要完成的任务由 ``TODO:'' 开头
# 你可以用 ``grep -rn "TODO"'' 快速找到它们

# 无需修改以下内容
TOOL_DIR := tool
TOOL_IMAGE_NAME := ai-remover
INPUT_DIR := input_images
OUTPUT_DIR := output_images
ZIP_FILE := processed_images.zip
INPUT_FILES := $(shell find $(INPUT_DIR) -name '*.jpg')
OUTPUT_FILES := $(patsubst $(INPUT_DIR)/%.jpg,$(OUTPUT_DIR)/%.png,$(INPUT_FILES))

# 标记文件
DOCKER_BUILD_MARKER := .docker_image_built

.PHONY: all clean process package build shell

all: package

package: $(ZIP_FILE)
	@echo "压缩包已生成在 $(ZIP_FILE)"

$(ZIP_FILE): $(OUTPUT_FILES)
	@cd $(OUTPUT_DIR) && zip -j ../$(ZIP_FILE) *.png

process: $(OUTPUT_FILES)
	@echo "图片都已处理完毕"

clean:
	@rm -rf $(OUTPUT_DIR) $(ZIP_FILE) $(DOCKER_BUILD_MARKER)
	@echo "清理完成"

build: $(DOCKER_BUILD_MARKER)

$(DOCKER_BUILD_MARKER): $(TOOL_DIR)/Dockerfile $(TOOL_DIR)/main.py $(TOOL_DIR)/requirements.txt
	@docker build -t $(TOOL_IMAGE_NAME) $(TOOL_DIR)
	@touch $(DOCKER_BUILD_MARKER)

$(OUTPUT_DIR)/%.png: $(INPUT_DIR)/%.jpg $(DOCKER_BUILD_MARKER)
	@echo "正在处理 $< -> $@ "
	@mkdir -p $(dir $@)
	@docker run --rm \
		-v "$(CURDIR)/$(INPUT_DIR):/app/$(INPUT_DIR)" \
		-v "$(CURDIR)/$(OUTPUT_DIR):/app/$(OUTPUT_DIR)" \
		-e U2NET_HOME=/app/models \
		$(TOOL_IMAGE_NAME) \
		--input "/app/$<" --output "/app/$@"

shell: $(DOCKER_BUILD_MARKER)
	@docker run --rm -it \
		-v "$(CURDIR)/input_images:/app/input_images" \
		-v "$(CURDIR)/output_images:/app/output_images" \
		--entrypoint /bin/bash \
		$(TOOL_IMAGE_NAME)
