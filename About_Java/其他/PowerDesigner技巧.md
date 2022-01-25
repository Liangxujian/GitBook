# PowerDesigner技巧

## 一、生成html版表清单文档

![153227199](.\assets\30153227199.png)

![153526595](.\assets\30153526595.png)

![153654044](.\assets\30153654044.png)

![30154238133](.\assets\30154238133.png)

![30154438502](.\assets\30154438502.png)

## 二、表名以及字段名全部转成小写

UCase - 大写；LCase - 小写

````bash
Option Explicit  
ValidationMode = True  
InteractiveMode = im_Batch  
Dim mdl ' 当前模型  
' 获取当前模型  
Set mdl = ActiveModel  
If (mdl Is Nothing) Then  
   MsgBox "没有打开一个模型" 
ElseIf Not mdl.IsKindOf(PdPDM.cls_Model) Then  
   MsgBox "当前模型不是一个PDM" 
Else  
'调用处理程序  
   ProcessFolder mdl  
End If    
'调用的处理程序  
Private sub ProcessFolder(folder)  
   Dim Tab '要处理的表  
   for each Tab in folder.Tables  
    ' if not Tab.isShortcut then  
        ' Tab.code = tab.name  
        '表名处理，前边添加前缀，字母小写  
        Tab.name=  UCase(Tab.name)  
        Tab.code= UCase(Tab.code)  
         Dim col ' 要处理的列  
         for each col in Tab.columns  
            '列名称和code全部小写，大写诗UCase  
            col.code= UCase(col.code)  
            col.name= UCase(col.name)  
         next  
      'end if 
   next    
' 处理视图  
'  Dim view 'running view  
'   for each view in folder.Views  
   '   if not view.isShortcut then  
       '  view.code = view.name  
    '  end if 
  ' next     
   ' 递归进入 sub-packages  
   Dim f ' sub  folder  
   For Each f In folder.Packages  
      if not f.IsShortcut then  
         ProcessFolder f  
      end if 
   Next  
end sub
````

Tools – Excute Commands – Edit/Run Script 或者 使用快捷键 Ctrl+Shift+X，调出脚本执行窗口，复制脚本并执行