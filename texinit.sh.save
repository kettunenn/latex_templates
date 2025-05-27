#!/bin/bash

# Dependencies:
# - ImageMagick (for 'convert')
# - viu (for terminal image preview)
#
# Optional:
# - a terminal that supports image display (e.g. kitty, iTerm2, wezterm)
#
# Install on Arch-based systems:
#   sudo pacman -S imagemagick viu

# Path to your LaTeX template folder (change this to the real path)
TEMPLATE_DIR="$HOME/Documents/latex_templates"
TEMP_IMAGE="/tmp/preview_template.png"

while true; do
    # Get only subdirectories
    template_dirs=()
    for dir in "$TEMPLATE_DIR"/*/; do
        [ -d "$dir" ] && template_dirs+=("$(basename "$dir")")
    done

    # If no templates found
    if [ ${#template_dirs[@]} -eq 0 ]; then
        echo "No template directories found in $TEMPLATE_DIR"
        exit 1
    fi

    echo "Available LaTeX templates:"
    select template in "${template_dirs[@]}"; do
        if [[ -n "$template" ]]; then
            TEMPLATE_PATH="$TEMPLATE_DIR/$template"
            break
        else
            echo "Invalid selection. Please try again."
        fi
    done


    # Ask for preview
    read -p "Preview this template? [y/N]: " preview_choice
    preview_choice=${preview_choice,,}  # Convert to lowercase

    if [[ "$preview_choice" == "y" ]]; then
        # Look for a PDF file in the top-level of the selected template directory
        pdf_file=$(find "$TEMPLATE_PATH" -maxdepth 1 -type f -iname "*.pdf" | head -n 1)

        if [[ -z "$pdf_file" ]]; then
            echo "No PDF found in $TEMPLATE_PATH to preview."
        else
            echo "Generating preview..."
            magick -density 100 "$pdf_file[0]" -background white -flatten "$TEMP_IMAGE"

            if [[ -f "$TEMP_IMAGE" ]]; then
                viu "$TEMP_IMAGE"
                rm -f "$TEMP_IMAGE"
            else
                echo "Failed to generate preview image."
            fi

            # Confirm use
            read -p "Use this template? [Y/n]: " confirm
            confirm=${confirm,,}

            if [[ "$confirm" != "n" ]]; then
                break  # Exit preview loop and continue with project creation
            fi

        fi
    else 
        break  # Exit preview loop and continue with project creation
    fi


done



# Ask for project name (optional)
read -p "Enter name for new project directory (default is "latex"): " PROJECT_NAME

# Set target directory
if [ -z "$PROJECT_NAME" ]; then
    PROJECT_NAME=latex
fi

mkdir -p "$PROJECT_NAME"
TARGET_DIR="$PROJECT_NAME"

# Copy template to target directory
cp -r "$TEMPLATE_PATH/"* "$TARGET_DIR"

echo "New LaTeX project created in: $TARGET_DIR"
