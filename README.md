# ＭIUI主题冷启动优化总结

１ 子线程启动Sercive或者广播，会回到MainThread和系统进程通信，block启动流程

２　Fragment懒加载技术

３　首次LayoutInflater　recyclerview　xml耗时严重
