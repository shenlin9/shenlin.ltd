# VimFiler

title: VimFiler
categories:
  - Vim
  - VimFiler
tags:
  - Vim
  - VimFiler

---

## 配置项

定义vimfiler位置 topleft or rightbelow
```
let g:vimfiler_direction = 'topleft'
```

自动切换到编辑文件的目录
```
let g:vimfiler_enable_auto_cd = 1
```

## 快捷键

    TAB         切换窗口
    S           排序，排序选项里有 save 功能，可以保存排序选项
    t           展开目录
    T           递归展开目录
    <S-SPC>     标记文件或目录
    q			隐藏

    *			<Plug>(vimfiler_toggle_mark_all_lines)
    #			<Plug>(vimfiler_mark_similar_lines)
    U			<Plug>(vimfiler_clear_mark_all_lines)
    c			<Plug>(vimfiler_copy_file)
    m			<Plug>(vimfiler_move_file)
    d			<Plug>(vimfiler_delete_file)
    Cc			<Plug>(vimfiler_clipboard_copy_file)
    Cm			<Plug>(vimfiler_clipboard_move_file)
    Cp			<Plug>(vimfiler_clipboard_paste)
    r			<Plug>(vimfiler_rename_file)
    K			<Plug>(vimfiler_make_directory)
    N			<Plug>(vimfiler_new_file)
    <Enter>			<Plug>(vimfiler_cd_or_edit)
    o			<Plug>(vimfiler_expand_or_edit)
    l			<Plug>(vimfiler_smart_l)
    x			<Plug>(vimfiler_execute_system_associated)
    X			<Plug>(vimfiler_execute_vimfiler_associated)
    h			<Plug>(vimfiler_smart_h)
    L			<Plug>(vimfiler_switch_to_drive)
    ~			<Plug>(vimfiler_switch_to_home_directory)
    \			<Plug>(vimfiler_switch_to_root_directory)
    &			<Plug>(vimfiler_switch_to_project_directory)
    <C-j>			<Plug>(vimfiler_switch_to_history_directory)
    <BS>			<Plug>(vimfiler_switch_to_parent_directory)
    .			<Plug>(vimfiler_toggle_visible_ignore_files)
    H			<Plug>(vimfiler_popup_shell)
    e			<Plug>(vimfiler_edit_file)
    E			<Plug>(vimfiler_split_edit_file)
    B			<Plug>(vimfiler_edit_binary_file)
    ge			<Plug>(vimfiler_execute_external_filer)
    <RightMouse>		<Plug>(vimfiler_execute_external_filer)
    !			<Plug>(vimfiler_execute_shell_command)
    Q			<Plug>(vimfiler_exit)
    -			<Plug>(vimfiler_close)
    g?			<Plug>(vimfiler_help)
    v			<Plug>(vimfiler_preview_file)
    O			<Plug>(vimfiler_sync_with_current_vimfiler)
    go			<Plug>(vimfiler_open_file_in_another_vimfiler)
    <C-g>			<Plug>(vimfiler_print_filename)
    g<C-g>			<Plug>(vimfiler_toggle_maximize_window)
    yy			<Plug>(vimfiler_yank_full_path)
    M			<Plug>(vimfiler_set_current_mask)
    gr			<Plug>(vimfiler_grep)
    gf			<Plug>(vimfiler_find)
    S			<Plug>(vimfiler_select_sort_type)
    <C-v>			<Plug>(vimfiler_switch_vim_buffer_mode)
    gc			<Plug>(vimfiler_cd_vim_current_dir)
    gs			<Plug>(vimfiler_toggle_safe_mode)
    gS			<Plug>(vimfiler_toggle_simple_mode)
    a			<Plug>(vimfiler_choose_action)
    Y			<Plug>(vimfiler_pushd)
    P			<Plug>(vimfiler_popd)
    t			<Plug>(vimfiler_expand_tree)
    T			<Plug>(vimfiler_expand_tree_recursive)
    I			<Plug>(vimfiler_cd_input_directory)
    <2-LeftMouse>		<Plug>(vimfiler_double_click)
    gj			<Plug>(vimfiler_jump_last_child)
    gk			<Plug>(vimfiler_jump_first_child)
