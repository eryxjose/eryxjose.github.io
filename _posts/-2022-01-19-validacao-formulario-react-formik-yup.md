




    const validationSchema = Yup.object().shape({
        cursoId: Yup.string().required(
        "Selecione um curso. O campo Curso é obrigatório."
        ),
        inicioInscricao: Yup.date()
        .min(
            startDate,
            "O início da inscrição deve ser posterior ao dia de hoje."
        )
        .required("Informe a data de início do período de inscrição."),
        terminoInscricao: Yup.date()
        .when("inicioInscricao", (inicioInscricao, schema) =>
            moment(inicioInscricao).isValid()
            ? schema.min(
                inicioInscricao,
                "O término precisa ser posterior ao início da inscrição."
                )
            : schema
        )
        .required("Informe a data de término do período de inscrição."),
        inicioAnaliseInscricao: Yup.date()
        .when("terminoInscricao", (terminoInscricao, schema) =>
            moment(terminoInscricao).isValid()
            ? schema.min(
                terminoInscricao,
                "O início da análise precisa ser posterior ao término da inscrição."
                )
            : schema
        )
        .required(
            "Informe a data de início do período de análise de inscrições."
        ),
        terminoAnaliseInscricao: Yup.date()
        .when("inicioAnaliseInscricao", (inicioAnaliseInscricao, schema) =>
            moment(inicioAnaliseInscricao).isValid()
            ? schema.min(
                inicioAnaliseInscricao,
                "O término precisa ser posterior ao início da análise."
                )
            : schema
        )
        .required(
            "Informe a data de término do período de análise de inscrições."
        ),
        inicioCurso: Yup.date()
        .when("terminoAnaliseInscricao", (terminoAnaliseInscricao, schema) =>
            moment(terminoAnaliseInscricao).isValid()
            ? schema.min(
                terminoAnaliseInscricao,
                "O início do curso precisa ser posterior ao término da análise."
                )
            : schema
        )
        .required("Informe a data de início da turma."),
        terminoCurso: Yup.date()
        .when("inicioCurso", (inicioCurso, schema) =>
            moment(inicioCurso).isValid()
            ? schema.min(
                inicioCurso,
                "O término do curso precisa ser posterior ao início."
                )
            : schema
        )
        .required("Informe a data de término da turma."),
    });


# Referências

https://www.techzaion.com/validation-with-yup
https://github.com/jquense/yup
https://formik.org/docs/guides/validation
https://stackoverflow.com/questions/49394391/conditional-validation-in-yup
https://reactdatepicker.com/#example-calendar-start-day
https://momentjs.com/
https://stackoverflow.com/questions/54888337/how-to-validate-min-age-with-yup-and-moment-js
https://formik.org/docs/api/formik
