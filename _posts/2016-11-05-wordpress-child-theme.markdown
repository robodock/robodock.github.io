---
layout: post
title: "Wordpress 建立 child theme"
date: 2016-11-05 10:20
---

**Wordpress 中的 child theme 可繼承 parent theme 的所有功能與樣式，但又可同時自訂修改自有樣式。
在網站中使用 child theme 可避免當 parent theme 更新時，影響到自訂部分。**

### 建立 child theme ###

登入網頁伺服器主機，找到網站中的 theme 目錄，預設位置在 `wp-content/themes` ，建立 child theme 子目錄，慣例可用 parenttheme-child 做名稱。此目錄底下則需要兩個檔案，`style.css`、`functions.php`。

- **child theme directory**
    - **style.css**
    - **functions.php**

`style.css` 檔案內容必須開頭如下：

    /*
     Theme Name:   Twenty Fifteen Child
     Theme URI:    http://example.com/twenty-fifteen-child/
     Description:  Twenty Fifteen Child Theme
     Author:       John Doe
     Author URI:   http://example.com
     Template:     twentyfifteen
     Version:      1.0.0
     License:      GNU General Public License v2 or later
     License URI:  http://www.gnu.org/licenses/gpl-2.0.html
     Tags:         light, dark, two-columns, right-sidebar, responsive-layout, accessibility-ready
     Text Domain:  twenty-fifteen-child
    */
 
其中 `Template` 為繼承對應的 parent theme，其他則依狀況修改對應自己的網站設定。

`functions.php` 則用來引入 stylesheets，內容如下：

    <?php
    function my_theme_enqueue_styles() {
    
        $parent_style = 'parent-style'; // This is 'twentyfifteen-style' for the Twenty Fifteen theme.
    
        wp_enqueue_style( $parent_style, get_template_directory_uri() . '/style.css' );
        wp_enqueue_style( 'child-style',
            get_stylesheet_directory_uri() . '/style.css',
            array( $parent_style ),
            wp_get_theme()->get('Version')
        );
    }
    add_action( 'wp_enqueue_scripts', 'my_theme_enqueue_styles' );
    ?>

如果網站中使用多個 .css 檔，則必須分別加入 function 中。

完成後即可在 Wordpress 佈景主題頁面中選取新增的 child theme 使用。