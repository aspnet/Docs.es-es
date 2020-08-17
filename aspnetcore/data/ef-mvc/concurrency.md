---
title: 'Tutorial: Control de simultaneidad: ASP.NET MVC con EF Core'
description: Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan la misma entidad al mismo tiempo.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/concurrency
ms.openlocfilehash: f97d551348ca31cc35ab5b04493ea702311a069e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012963"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="c8036-103">Tutorial: Control de simultaneidad: ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="c8036-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="c8036-104">En los tutoriales anteriores, aprendió a actualizar los datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="c8036-105">Este tutorial muestra cómo tratar los conflictos cuando varios usuarios actualizan la misma entidad al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="c8036-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="c8036-106">Podrá crear páginas web que funcionan con la entidad Department y controlan los errores de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="c8036-107">Las siguientes ilustraciones muestran las páginas Edit y Delete, incluidos algunos mensajes que se muestran si se produce un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Página Edit Department](concurrency/_static/edit-error.png)

![Página Department Delete](concurrency/_static/delete-error.png)

<span data-ttu-id="c8036-110">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="c8036-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c8036-111">Obtiene información sobre los conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="c8036-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="c8036-112">Agrega una propiedad de seguimiento</span><span class="sxs-lookup"><span data-stu-id="c8036-112">Add a tracking property</span></span>
> * <span data-ttu-id="c8036-113">Crea un controlador y vistas de Departments</span><span class="sxs-lookup"><span data-stu-id="c8036-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="c8036-114">Actualiza la vista de índice</span><span class="sxs-lookup"><span data-stu-id="c8036-114">Update Index view</span></span>
> * <span data-ttu-id="c8036-115">Actualiza los métodos de edición</span><span class="sxs-lookup"><span data-stu-id="c8036-115">Update Edit methods</span></span>
> * <span data-ttu-id="c8036-116">Actualiza la vista de edición</span><span class="sxs-lookup"><span data-stu-id="c8036-116">Update Edit view</span></span>
> * <span data-ttu-id="c8036-117">Prueba los conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="c8036-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="c8036-118">Actualizar la página Delete</span><span class="sxs-lookup"><span data-stu-id="c8036-118">Update the Delete page</span></span>
> * <span data-ttu-id="c8036-119">Actualizar las vistas Details y Create</span><span class="sxs-lookup"><span data-stu-id="c8036-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c8036-120">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="c8036-120">Prerequisites</span></span>

* [<span data-ttu-id="c8036-121">Actualización de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="c8036-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="c8036-122">Conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="c8036-122">Concurrency conflicts</span></span>

<span data-ttu-id="c8036-123">Los conflictos de simultaneidad ocurren cuando un usuario muestra los datos de una entidad para editarlos y, después, otro usuario actualiza los datos de la misma entidad antes de que el primer cambio del usuario se escriba en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="c8036-124">Si no habilita la detección de este tipo de conflictos, quien actualice la base de datos en último lugar sobrescribe los cambios del otro usuario.</span><span class="sxs-lookup"><span data-stu-id="c8036-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="c8036-125">En muchas aplicaciones, el riesgo es aceptable: si hay pocos usuarios o pocas actualizaciones, o si no es realmente importante si se sobrescriben algunos cambios, el costo de programación para la simultaneidad puede superar el beneficio obtenido.</span><span class="sxs-lookup"><span data-stu-id="c8036-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="c8036-126">En ese caso, no tendrá que configurar la aplicación para que controle los conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="c8036-127">Simultaneidad pesimista (bloqueo)</span><span class="sxs-lookup"><span data-stu-id="c8036-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="c8036-128">Si la aplicación necesita evitar la pérdida accidental de datos en casos de simultaneidad, una manera de hacerlo es usar los bloqueos de base de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="c8036-129">Esto se denomina simultaneidad pesimista.</span><span class="sxs-lookup"><span data-stu-id="c8036-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="c8036-130">Por ejemplo, antes de leer una fila de una base de datos, solicita un bloqueo de solo lectura o para acceso de actualización.</span><span class="sxs-lookup"><span data-stu-id="c8036-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="c8036-131">Si bloquea una fila para acceso de actualización, no se permite que ningún otro usuario bloquee la fila como solo lectura o para acceso de actualización, porque recibirían una copia de los datos que se están modificando.</span><span class="sxs-lookup"><span data-stu-id="c8036-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="c8036-132">Si bloquea una fila para acceso de solo lectura, otras personas también pueden bloquearla para acceso de solo lectura pero no para actualización.</span><span class="sxs-lookup"><span data-stu-id="c8036-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="c8036-133">Administrar los bloqueos tiene desventajas.</span><span class="sxs-lookup"><span data-stu-id="c8036-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="c8036-134">Puede ser bastante complicado de programar.</span><span class="sxs-lookup"><span data-stu-id="c8036-134">It can be complex to program.</span></span> <span data-ttu-id="c8036-135">Se necesita un número significativo de recursos de administración de base de datos, y puede provocar problemas de rendimiento a medida que aumenta el número de usuarios de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="c8036-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="c8036-136">Por estos motivos, no todos los sistemas de administración de bases de datos admiten la simultaneidad pesimista.</span><span class="sxs-lookup"><span data-stu-id="c8036-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="c8036-137">Entity Framework Core no proporciona ninguna compatibilidad integrada para ello y en este tutorial no se muestra cómo implementarla.</span><span class="sxs-lookup"><span data-stu-id="c8036-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="c8036-138">Simultaneidad optimista</span><span class="sxs-lookup"><span data-stu-id="c8036-138">Optimistic Concurrency</span></span>

<span data-ttu-id="c8036-139">La alternativa a la simultaneidad pesimista es la simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="c8036-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="c8036-140">La simultaneidad optimista implica permitir que se produzcan conflictos de simultaneidad y reaccionar correctamente si ocurren.</span><span class="sxs-lookup"><span data-stu-id="c8036-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="c8036-141">Por ejemplo, Jane visita la página Edit Department y cambia la cantidad de Budget para el departamento de inglés de 350.000,00 a 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="c8036-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Cambiar el presupuesto a 0](concurrency/_static/change-budget.png)

<span data-ttu-id="c8036-143">Antes de que Jane haga clic en **Save**, John visita la misma página y cambia el campo Start Date de 9/1/2007 a 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="c8036-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Cambiar la fecha de inicio a 2013](concurrency/_static/change-date.png)

<span data-ttu-id="c8036-145">Jane hace clic en **Save** primero y ve su cambio cuando el explorador vuelve a la página de índice.</span><span class="sxs-lookup"><span data-stu-id="c8036-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Presupuesto cambiado a cero](concurrency/_static/budget-zero.png)

<span data-ttu-id="c8036-147">Entonces, John hace clic en **Save** en una página Edit que sigue mostrando un presupuesto de 350.000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="c8036-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="c8036-148">Lo que sucede después viene determinado por cómo controla los conflictos de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="c8036-149">Algunas de las opciones se exponen a continuación:</span><span class="sxs-lookup"><span data-stu-id="c8036-149">Some of the options include the following:</span></span>

* <span data-ttu-id="c8036-150">Puede realizar un seguimiento de la propiedad que ha modificado un usuario y actualizar solo las columnas correspondientes de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="c8036-151">En el escenario de ejemplo, no se perdería ningún dato porque los dos usuarios actualizaron diferentes propiedades.</span><span class="sxs-lookup"><span data-stu-id="c8036-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="c8036-152">La próxima vez que un usuario examine el departamento de inglés, verá los cambios tanto de Jane como de John: una fecha de inicio de 9/1/2013 y un presupuesto de cero dólares.</span><span class="sxs-lookup"><span data-stu-id="c8036-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="c8036-153">Este método de actualización puede reducir el número de conflictos que pueden dar lugar a una pérdida de datos, pero no puede evitar la pérdida de datos si se realizan cambios paralelos a la misma propiedad de una entidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="c8036-154">Si Entity Framework funciona de esta manera o no, depende de cómo implemente el código de actualización.</span><span class="sxs-lookup"><span data-stu-id="c8036-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="c8036-155">A menudo no resulta práctico en una aplicación web, porque puede requerir mantener grandes cantidades de estado con el fin de realizar un seguimiento de todos los valores de propiedad originales de una entidad, así como los valores nuevos.</span><span class="sxs-lookup"><span data-stu-id="c8036-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="c8036-156">Mantener grandes cantidades de estado puede afectar al rendimiento de la aplicación porque requiere recursos del servidor o se deben incluir en la propia página web (por ejemplo, en campos ocultos) o en una cookie.</span><span class="sxs-lookup"><span data-stu-id="c8036-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="c8036-157">Puede permitir que los cambios de John sobrescriban los cambios de Jane.</span><span class="sxs-lookup"><span data-stu-id="c8036-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="c8036-158">La próxima vez que un usuario examine el departamento de inglés, verá 9/1/2013 y el valor de 350.000,00 USD restaurado.</span><span class="sxs-lookup"><span data-stu-id="c8036-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="c8036-159">Esto se denomina un escenario de *Prevalece el cliente* o *Prevalece el último*.</span><span class="sxs-lookup"><span data-stu-id="c8036-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="c8036-160">(Todos los valores del cliente tienen prioridad sobre lo que aparece en el almacén de datos). Como se mencionó en la introducción de esta sección, si no hace ninguna codificación para el control de la simultaneidad, se realizará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="c8036-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="c8036-161">Puede evitar que el cambio de John se actualice en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="c8036-162">Por lo general, debería mostrar un mensaje de error, mostrarle el estado actual de los datos y permitirle volver a aplicar los cambios si quiere realizarlos.</span><span class="sxs-lookup"><span data-stu-id="c8036-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="c8036-163">Esto se denomina un escenario de *Prevalece el almacén*.</span><span class="sxs-lookup"><span data-stu-id="c8036-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="c8036-164">(Los valores del almacén de datos tienen prioridad sobre los valores enviados por el cliente). En este tutorial implementará el escenario de Prevalece el almacén.</span><span class="sxs-lookup"><span data-stu-id="c8036-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="c8036-165">Este método garantiza que ningún cambio se sobrescriba sin que se avise al usuario de lo que está sucediendo.</span><span class="sxs-lookup"><span data-stu-id="c8036-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="c8036-166">Detectar los conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="c8036-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="c8036-167">Puede resolver los conflictos controlando las excepciones `DbConcurrencyException` que inicia Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="c8036-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="c8036-168">Para saber cuándo se producen dichas excepciones, Entity Framework debe ser capaz de detectar conflictos.</span><span class="sxs-lookup"><span data-stu-id="c8036-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="c8036-169">Por lo tanto, debe configurar correctamente la base de datos y el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="c8036-170">Algunas opciones para habilitar la detección de conflictos son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="c8036-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="c8036-171">En la tabla de la base de datos, incluya una columna de seguimiento que pueda usarse para determinar si una fila ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="c8036-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="c8036-172">Después puede configurar Entity Framework para que incluya esa columna en la cláusula Where de los comandos Update o Delete de SQL.</span><span class="sxs-lookup"><span data-stu-id="c8036-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="c8036-173">El tipo de datos de la columna de seguimiento suele ser `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="c8036-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="c8036-174">El valor `rowversion` es un número secuencial que se incrementa cada vez que se actualiza la fila.</span><span class="sxs-lookup"><span data-stu-id="c8036-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="c8036-175">En un comando Update o Delete, la cláusula Where incluye el valor original de la columna de seguimiento (la versión de la fila original).</span><span class="sxs-lookup"><span data-stu-id="c8036-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="c8036-176">Si otro usuario ha cambiado la fila que se está actualizando, el valor en la columna `rowversion` es diferente del valor original, por lo que la instrucción Update o Delete no puede encontrar la fila que se va a actualizar debido a la cláusula Where.</span><span class="sxs-lookup"><span data-stu-id="c8036-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="c8036-177">Cuando Entity Framework encuentra que no se ha actualizado ninguna fila mediante el comando Update o Delete (es decir, cuando el número de filas afectadas es cero), lo interpreta como un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="c8036-178">Configure Entity Framework para que incluya los valores originales de cada columna de la tabla en la cláusula Where de los comandos Update y Delete.</span><span class="sxs-lookup"><span data-stu-id="c8036-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="c8036-179">Como se muestra en la primera opción, si algo en la fila ha cambiado desde que se leyó por primera, la cláusula Where no devolverá una fila para actualizar, lo cual Entity Framework interpreta como un conflicto de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="c8036-180">Para las tablas de base de datos que tienen muchas columnas, este enfoque puede dar lugar a cláusulas Where muy grandes y puede requerir mantener grandes cantidades de estado.</span><span class="sxs-lookup"><span data-stu-id="c8036-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="c8036-181">Tal y como se indicó anteriormente, el mantenimiento de grandes cantidades de estado puede afectar al rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c8036-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="c8036-182">Por tanto, generalmente este enfoque no se recomienda y no es el método usado en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="c8036-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="c8036-183">Si quiere implementar este enfoque para la simultaneidad, tendrá que marcar todas las propiedades de clave no principal de la entidad de la que quiere realizar un seguimiento de simultaneidad agregándoles el atributo `ConcurrencyCheck`.</span><span class="sxs-lookup"><span data-stu-id="c8036-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="c8036-184">Este cambio permite que Entity Framework incluya todas las columnas en la cláusula Where de SQL de las instrucciones Update y Delete.</span><span class="sxs-lookup"><span data-stu-id="c8036-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="c8036-185">En el resto de este tutorial agregará una propiedad de seguimiento `rowversion` para la entidad Department, creará un controlador y vistas, y comprobará que todo funciona correctamente.</span><span class="sxs-lookup"><span data-stu-id="c8036-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="c8036-186">Agrega una propiedad de seguimiento</span><span class="sxs-lookup"><span data-stu-id="c8036-186">Add a tracking property</span></span>

<span data-ttu-id="c8036-187">En *Models/Department.cs*, agregue una propiedad de seguimiento denominada RowVersion:</span><span class="sxs-lookup"><span data-stu-id="c8036-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="c8036-188">El atributo `Timestamp` especifica que esta columna se incluirá en la cláusula Where de los comandos Update y Delete enviados a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="c8036-189">El atributo se denomina `Timestamp` porque las versiones anteriores de SQL Server usaban un tipo de datos `timestamp` antes de que la `rowversion` de SQL lo sustituyera por otro.</span><span class="sxs-lookup"><span data-stu-id="c8036-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="c8036-190">El tipo .NET de `rowversion` es una matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="c8036-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="c8036-191">Si prefiere usar la API fluida, puede usar el método `IsConcurrencyToken` (en *Data/SchoolContext.cs*) para especificar la propiedad de seguimiento, tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c8036-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="c8036-192">Al agregar una propiedad cambió el modelo de base de datos, por lo que necesita realizar otra migración.</span><span class="sxs-lookup"><span data-stu-id="c8036-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="c8036-193">Guarde los cambios, compile el proyecto y, después, escriba los siguientes comandos en la ventana de comandos:</span><span class="sxs-lookup"><span data-stu-id="c8036-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="c8036-194">Crea un controlador y vistas de Departments</span><span class="sxs-lookup"><span data-stu-id="c8036-194">Create Departments controller and views</span></span>

<span data-ttu-id="c8036-195">Aplique la técnica scaffolding a un controlador y vistas de Departments como lo hizo anteriormente para Students, Courses e Instructors.</span><span class="sxs-lookup"><span data-stu-id="c8036-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Aplicar la técnica scaffolding a Department](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="c8036-197">En el archivo *DepartmentsController.cs*, cambie las cuatro repeticiones de "FirstMidName" a "FullName" para que las listas desplegables del administrador del departamento contengan el nombre completo del instructor en lugar de simplemente el apellido.</span><span class="sxs-lookup"><span data-stu-id="c8036-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="c8036-198">Actualiza la vista de índice</span><span class="sxs-lookup"><span data-stu-id="c8036-198">Update Index view</span></span>

<span data-ttu-id="c8036-199">El motor de scaffolding creó una columna RowVersion en la vista Index, pero ese campo no debería mostrarse.</span><span class="sxs-lookup"><span data-stu-id="c8036-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="c8036-200">Reemplace el código de *Views/Departments/Index.cshtml* con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="c8036-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="c8036-201">Esto cambia el encabezado por "Departments", elimina la columna RowVersion y muestra el nombre completo en lugar del nombre del administrador.</span><span class="sxs-lookup"><span data-stu-id="c8036-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="c8036-202">Actualiza los métodos de edición</span><span class="sxs-lookup"><span data-stu-id="c8036-202">Update Edit methods</span></span>

<span data-ttu-id="c8036-203">En el método `Edit` de HttpGet y el método `Details`, agregue `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="c8036-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="c8036-204">En el método `Edit` de HttpGet, agregue carga diligente para el administrador.</span><span class="sxs-lookup"><span data-stu-id="c8036-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="c8036-205">Sustituya el código existente para el método `Edit` de HttpPost por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="c8036-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="c8036-206">El código comienza por intentar leer el departamento que se va a actualizar.</span><span class="sxs-lookup"><span data-stu-id="c8036-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="c8036-207">Si el método `FirstOrDefaultAsync` devuelve NULL, otro usuario eliminó el departamento.</span><span class="sxs-lookup"><span data-stu-id="c8036-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="c8036-208">En ese caso, el código usa los valores del formulario expuesto para crear una entidad Department, por lo que puede volver a mostrarse la página Edit con un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="c8036-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="c8036-209">Como alternativa, no tendrá que volver a crear la entidad Department si solo muestra un mensaje de error sin volver a mostrar los campos del departamento.</span><span class="sxs-lookup"><span data-stu-id="c8036-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="c8036-210">La vista almacena el valor `RowVersion` original en un campo oculto, y este método recibe ese valor en el parámetro `rowVersion`.</span><span class="sxs-lookup"><span data-stu-id="c8036-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="c8036-211">Antes de llamar a `SaveChanges`, tendrá que colocar dicho valor de propiedad `RowVersion` original en la colección `OriginalValues` para la entidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="c8036-212">Cuando Entity Framework crea un comando UPDATE de SQL, ese comando incluirá una cláusula WHERE que comprueba si hay una fila que tenga el valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="c8036-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="c8036-213">Si no hay ninguna fila afectada por el comando UPDATE (ninguna fila tiene el valor `RowVersion` original), Entity Framework inicia una excepción `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="c8036-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="c8036-214">El código del bloque catch de esa excepción obtiene la entidad Department afectada que tiene los valores actualizados de la propiedad `Entries` del objeto de excepción.</span><span class="sxs-lookup"><span data-stu-id="c8036-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="c8036-215">La colección `Entries` contará con un solo objeto `EntityEntry`.</span><span class="sxs-lookup"><span data-stu-id="c8036-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="c8036-216">Puede usar dicho objeto para obtener los nuevos valores especificados por el usuario y los valores actuales de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="c8036-217">El código agrega un mensaje de error personalizado para cada columna que tenga valores de base de datos diferentes de lo que el usuario especificó en la página Edit (aquí solo se muestra un campo por razones de brevedad).</span><span class="sxs-lookup"><span data-stu-id="c8036-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="c8036-218">Por último, el código establece el valor `RowVersion` de `departmentToUpdate` para el nuevo valor recuperado de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="c8036-219">Este nuevo valor `RowVersion` se almacenará en el campo oculto cuando se vuelva a mostrar la página Edit y, la próxima vez que el usuario haga clic en **Save**, solo se detectarán los errores de simultaneidad que se produzcan desde que se vuelva a mostrar la página Edit.</span><span class="sxs-lookup"><span data-stu-id="c8036-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="c8036-220">La instrucción `ModelState.Remove` es necesaria porque `ModelState` tiene el valor `RowVersion` antiguo.</span><span class="sxs-lookup"><span data-stu-id="c8036-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="c8036-221">En la vista, el valor `ModelState` de un campo tiene prioridad sobre los valores de propiedad de modelo cuando ambos están presentes.</span><span class="sxs-lookup"><span data-stu-id="c8036-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="c8036-222">Actualiza la vista de edición</span><span class="sxs-lookup"><span data-stu-id="c8036-222">Update Edit view</span></span>

<span data-ttu-id="c8036-223">En *Views/Departments/Edit.cshtml*, realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="c8036-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="c8036-224">Agregue un campo oculto para guardar el valor de propiedad `RowVersion`, inmediatamente después de un campo oculto para la propiedad `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="c8036-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="c8036-225">Agregue una opción "Select Administrator" a la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="c8036-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="c8036-226">Prueba los conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="c8036-226">Test concurrency conflicts</span></span>

<span data-ttu-id="c8036-227">Ejecute la aplicación y vaya a la página de índice de Departments.</span><span class="sxs-lookup"><span data-stu-id="c8036-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="c8036-228">Haga clic con el botón derecho en el hipervínculo **Edit** del departamento de inglés, seleccione **Abrir en nueva pestaña** y, después, haga clic en el hipervínculo **Edit** del departamento de inglés.</span><span class="sxs-lookup"><span data-stu-id="c8036-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="c8036-229">Las dos pestañas del explorador ahora muestran la misma información.</span><span class="sxs-lookup"><span data-stu-id="c8036-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="c8036-230">Cambie un campo en la primera pestaña del explorador y haga clic en **Save**.</span><span class="sxs-lookup"><span data-stu-id="c8036-230">Change a field in the first browser tab and click **Save**.</span></span>

![Página 1 de Department Edit después del cambio](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="c8036-232">El explorador muestra la página de índice con el valor modificado.</span><span class="sxs-lookup"><span data-stu-id="c8036-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="c8036-233">Cambie un campo en la segunda pestaña del explorador.</span><span class="sxs-lookup"><span data-stu-id="c8036-233">Change a field in the second browser tab.</span></span>

![Página 2 de Department Edit después del cambio](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="c8036-235">Haga clic en **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="c8036-235">Click **Save**.</span></span> <span data-ttu-id="c8036-236">Verá un mensaje de error:</span><span class="sxs-lookup"><span data-stu-id="c8036-236">You see an error message:</span></span>

![Mensaje de error de la página Department Edit](concurrency/_static/edit-error.png)

<span data-ttu-id="c8036-238">Vuelva a hacer clic en **Save**.</span><span class="sxs-lookup"><span data-stu-id="c8036-238">Click **Save** again.</span></span> <span data-ttu-id="c8036-239">Se guarda el valor especificado en la segunda pestaña del explorador.</span><span class="sxs-lookup"><span data-stu-id="c8036-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="c8036-240">Verá los valores guardados cuando aparezca la página de índice.</span><span class="sxs-lookup"><span data-stu-id="c8036-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="c8036-241">Actualizar la página Delete</span><span class="sxs-lookup"><span data-stu-id="c8036-241">Update the Delete page</span></span>

<span data-ttu-id="c8036-242">Para la página Delete, Entity Framework detecta los conflictos de simultaneidad causados por una persona que edita el departamento de forma similar.</span><span class="sxs-lookup"><span data-stu-id="c8036-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="c8036-243">Cuando el método `Delete` de HttpGet muestra la vista de confirmación, la vista incluye el valor `RowVersion` original en un campo oculto.</span><span class="sxs-lookup"><span data-stu-id="c8036-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="c8036-244">Dicho valor está entonces disponible para el método `Delete` de HttpPost al que se llama cuando el usuario confirma la eliminación.</span><span class="sxs-lookup"><span data-stu-id="c8036-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="c8036-245">Cuando Entity Framework crea el comando DELETE de SQL, incluye una cláusula WHERE con el valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="c8036-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="c8036-246">Si el comando tiene como resultado cero filas afectadas (es decir, la fila se cambió después de que se muestre la página de confirmación de eliminación), se produce una excepción de simultaneidad y el método `Delete` de HttpGet se llama con una marca de error establecida en true para volver a mostrar la página de confirmación con un mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="c8036-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="c8036-247">También es posible que se vieran afectadas cero filas porque otro usuario eliminó la fila, por lo que en ese caso no se muestra ningún mensaje de error.</span><span class="sxs-lookup"><span data-stu-id="c8036-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="c8036-248">Actualizar los métodos Delete en el controlador de Departments</span><span class="sxs-lookup"><span data-stu-id="c8036-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="c8036-249">En *DepartmentsController.cs*, reemplace el método `Delete` de HttpGet por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c8036-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="c8036-250">El método acepta un parámetro opcional que indica si la página volverá a aparecer después de un error de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="c8036-251">Si esta marca es true y el departamento especificado ya no existe, significa que otro usuario lo eliminó.</span><span class="sxs-lookup"><span data-stu-id="c8036-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="c8036-252">En ese caso, el código redirige a la página de índice.</span><span class="sxs-lookup"><span data-stu-id="c8036-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="c8036-253">Si esta marca es true y el departamento existe, significa que otro usuario lo modificó.</span><span class="sxs-lookup"><span data-stu-id="c8036-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="c8036-254">En ese caso, el código envía un mensaje de error a la vista mediante `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="c8036-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="c8036-255">Reemplace el código en el método `Delete` de HttpPost (denominado `DeleteConfirmed`) con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c8036-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="c8036-256">En el código al que se aplicó la técnica scaffolding que acaba de reemplazar, este método solo acepta un identificador de registro:</span><span class="sxs-lookup"><span data-stu-id="c8036-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="c8036-257">Ha cambiado este parámetro en una instancia de la entidad Department creada por el enlazador de modelos.</span><span class="sxs-lookup"><span data-stu-id="c8036-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="c8036-258">Esto proporciona a EF acceso al valor de la propiedad RowVersion, además de la clave de registro.</span><span class="sxs-lookup"><span data-stu-id="c8036-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="c8036-259">También ha cambiado el nombre del método de acción de `DeleteConfirmed` a `Delete`.</span><span class="sxs-lookup"><span data-stu-id="c8036-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="c8036-260">El código al que se aplicó la técnica scaffolding usa el nombre `DeleteConfirmed` para proporcionar al método HttpPost una firma única.</span><span class="sxs-lookup"><span data-stu-id="c8036-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="c8036-261">(El CLR requiere métodos sobrecargados para tener parámetros de método diferentes). Ahora que las firmas son únicas, puede ceñirse a la convención MVC y usar el mismo nombre para los métodos de eliminación de HttpPost y HttpGet.</span><span class="sxs-lookup"><span data-stu-id="c8036-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="c8036-262">Si ya se ha eliminado el departamento, el método `AnyAsync` devuelve false y la aplicación simplemente vuelve al método de índice.</span><span class="sxs-lookup"><span data-stu-id="c8036-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="c8036-263">Si se detecta un error de simultaneidad, el código vuelve a mostrar la página de confirmación de Delete y proporciona una marca que indica que se debería mostrar un mensaje de error de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="c8036-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="c8036-264">Actualizar la vista Delete</span><span class="sxs-lookup"><span data-stu-id="c8036-264">Update the Delete view</span></span>

<span data-ttu-id="c8036-265">En *Views/Departments/Delete.cshtml*, reemplace el código al que se aplicó la técnica scaffolding con el siguiente código, que agrega un campo de mensaje de error y campos ocultos para las propiedades DepartmentID y RowVersion.</span><span class="sxs-lookup"><span data-stu-id="c8036-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="c8036-266">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="c8036-266">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="c8036-267">Esto realiza los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="c8036-267">This makes the following changes:</span></span>

* <span data-ttu-id="c8036-268">Agrega un mensaje de error entre los encabezados `h2` y `h3`.</span><span class="sxs-lookup"><span data-stu-id="c8036-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="c8036-269">Reemplaza FirstMidName por FullName en el campo **Administrator**.</span><span class="sxs-lookup"><span data-stu-id="c8036-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="c8036-270">Quita el campo RowVersion.</span><span class="sxs-lookup"><span data-stu-id="c8036-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="c8036-271">Agrega un campo oculto para la propiedad `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="c8036-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="c8036-272">Ejecute la aplicación y vaya a la página de índice de Departments.</span><span class="sxs-lookup"><span data-stu-id="c8036-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="c8036-273">Haga clic con el botón derecho en el hipervínculo **Delete** del departamento de inglés, seleccione **Abrir en nueva pestaña** y, después, en la primera pestaña, haga clic en el hipervínculo **Edit** del departamento de inglés.</span><span class="sxs-lookup"><span data-stu-id="c8036-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="c8036-274">En la primera ventana, cambie uno de los valores y haga clic en **Save**:</span><span class="sxs-lookup"><span data-stu-id="c8036-274">In the first window, change one of the values, and click **Save**:</span></span>

![Página Department Edit después del cambio antes de eliminar](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="c8036-276">En la segunda pestaña, haga clic en **Delete**.</span><span class="sxs-lookup"><span data-stu-id="c8036-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="c8036-277">Verá el mensaje de error de simultaneidad y se actualizarán los valores de Department con lo que está actualmente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c8036-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Página de confirmación de Department Delete con error de simultaneidad](concurrency/_static/delete-error.png)

<span data-ttu-id="c8036-279">Si vuelve a hacer clic en **Delete**, se le redirigirá a la página de índice, que muestra que se ha eliminado el departamento.</span><span class="sxs-lookup"><span data-stu-id="c8036-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="c8036-280">Actualizar las vistas Details y Create</span><span class="sxs-lookup"><span data-stu-id="c8036-280">Update Details and Create views</span></span>

<span data-ttu-id="c8036-281">Si quiere, puede limpiar el código al que se ha aplicado la técnica scaffolding en las vistas Details y Create.</span><span class="sxs-lookup"><span data-stu-id="c8036-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="c8036-282">Reemplace el código de *Views/Departments/Details.cshtml* para eliminar la columna RowVersion y mostrar el nombre completo del administrador.</span><span class="sxs-lookup"><span data-stu-id="c8036-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="c8036-283">Reemplace el código de *Views/Departments/Create.cshtml* para agregar una opción Select en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="c8036-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="c8036-284">Obtención del código</span><span class="sxs-lookup"><span data-stu-id="c8036-284">Get the code</span></span>

[<span data-ttu-id="c8036-285">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="c8036-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="c8036-286">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c8036-286">Additional resources</span></span>

 <span data-ttu-id="c8036-287">Para obtener más información sobre cómo administrar la simultaneidad en EF Core, vea [Concurrency conflicts](/ef/core/saving/concurrency) (Conflictos de simultaneidad).</span><span class="sxs-lookup"><span data-stu-id="c8036-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c8036-288">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="c8036-288">Next steps</span></span>

<span data-ttu-id="c8036-289">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="c8036-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c8036-290">Obtenido información sobre los conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="c8036-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="c8036-291">Agregado una propiedad de seguimiento</span><span class="sxs-lookup"><span data-stu-id="c8036-291">Added a tracking property</span></span>
> * <span data-ttu-id="c8036-292">Creado un controlador y vistas de Departments</span><span class="sxs-lookup"><span data-stu-id="c8036-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="c8036-293">Actualizado la vista de índice</span><span class="sxs-lookup"><span data-stu-id="c8036-293">Updated Index view</span></span>
> * <span data-ttu-id="c8036-294">Actualizado los métodos de edición</span><span class="sxs-lookup"><span data-stu-id="c8036-294">Updated Edit methods</span></span>
> * <span data-ttu-id="c8036-295">Actualizado la vista de edición</span><span class="sxs-lookup"><span data-stu-id="c8036-295">Updated Edit view</span></span>
> * <span data-ttu-id="c8036-296">Probado los conflictos de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="c8036-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="c8036-297">Actualizado la página Delete</span><span class="sxs-lookup"><span data-stu-id="c8036-297">Updated the Delete page</span></span>
> * <span data-ttu-id="c8036-298">Actualizado las vistas Details y Create</span><span class="sxs-lookup"><span data-stu-id="c8036-298">Updated Details and Create views</span></span>

<span data-ttu-id="c8036-299">Pase al tutorial siguiente para obtener información sobre cómo implementar la herencia de tabla por jerarquía para las entidades Instructor y Student.</span><span class="sxs-lookup"><span data-stu-id="c8036-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c8036-300">Siguiente: Implementación de la herencia de tabla por jerarquía</span><span class="sxs-lookup"><span data-stu-id="c8036-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
