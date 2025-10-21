#Notion

Formula da Manus ->  if(Conclusão != null) { if(Conclusão > Deadline) { "Late Done" } else { "Done" } } else { if(Execução == true) { if(now() > Deadline) { "Hurry Up!" } else { "Working" } } else { if(now() > Deadline) { "Late" } else { "Waiting" } } }

formula para Notion

if(prop("Execução"),
    if(empty(prop("Conclusão")),
        if(prop("Deadline") < now(), "Hurry Up!", "Working"),
        if(prop("Conclusão") > prop("Deadline"), "Late Done", "Done")
    ),
    if(prop("Deadline") < now(), "Late", "Waiting")
)
